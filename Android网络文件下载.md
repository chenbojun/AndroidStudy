#下载文件：

##Retrofit声明下载文件的接口

```java
    /**
     * 下载某个主题
     *
     * @param url
     * @return
     */
    @Streaming
    @GET
    Call<ResponseBody> downloadTheme(@Url String url);
```

##下载处理：update、cancel、fail、save等
```java
public class ThemeDownLoader {
	private static final String LOG_TAG = "ThemeDownLoader";

	private DownloadCallback callback;
	private String url;
	private String checksum;
	private String zipPath;
	private String fileName;

	private volatile boolean isCanceled;

	public ThemeDownLoader(ThemeEO themeEO, DownloadCallback callback) {
		if (themeEO == null) {
			return;
		}
		this.callback = callback;
		this.url = themeEO.getZipUrl();
		this.checksum = themeEO.getMd5();
		this.zipPath = ThemeBL.getInstance().getThemeZipPath(themeEO);
		this.fileName = checksum + ".zip";
	}

	public void setCallback(DownloadCallback callback) {
		this.callback = callback;
	}


	public void cancel() {
		isCanceled = true;
	}

	public void download() {
		if (StringUtils.isBlank(url)) {
			if (callback != null) {
				callback.onFail();
			}
		}

		final Call<ResponseBody> call = ApiClient.instance().skinHttpApi().downloadTheme(url);

		call.enqueue(new Callback<ResponseBody>() {
			@Override
			public void onSuccess(ResponseBody responseBody) {
				handleResponse(responseBody);
			}

			@Override
			public void onFailure(CallError t) {
				if (callback != null) {
					callback.onFail();
				}
				LogUtil.i(LOG_TAG, "downloadTheme,failed");
			}
		});
	}

	private void handleResponse(final ResponseBody body) {
		Async.runOnIoThread(new Runnable() {
			@Override
			public void run() {
				boolean ret = saveToDisk(body);

				try {
					if (ret) {
						File zipFile = new File(zipPath + fileName);
						if (zipFile != null && zipFile.exists()) {
							//解压后的皮肤文件为/skin/themeId/appversion/version/themeId.skin
							ZipUtil.unZipFile(zipFile, zipPath + "/");
							zipFile.delete();
						}
					}
				} catch (IOException e) {
					e.printStackTrace();
					ret = false;
				}

				//下载失败或解压失败，删除之前文件
				if (!ret) {
					FileUtil.delAllFileOfDir(zipPath);
				}

				final boolean isSucess = ret;
				if (callback != null) {
					Async.postOnUiThread(new Runnable() {
						@Override
						public void run() {
							if (isCanceled) {
								callback.onCanceled();
							} else if (isSucess) {
								callback.onSucess();
							} else {
								callback.onFail();
								LogUtil.i(LOG_TAG, "handleResponse,failed");
							}
						}
					});
				}
			}
		});
	}

    // 保存下载文件
	private boolean saveToDisk(ResponseBody body) {
		//下载下来的文件保存为/skin/themeId/appversion/version/md5.zip
		File zipFile = FileUtil.createFile(zipPath, fileName);
		if (zipFile == null) {
			LogUtil.w(LOG_TAG, "saveToDisk,create file failed");
			return false;
		}

		InputStream is = null;
		OutputStream os = null;
		int bufferSize = Pools.SIZE_1K;
		byte[] buffer = Pools.acquireBuf(bufferSize);

		try {
			is = body.byteStream();
			os = new FileOutputStream(zipFile);
			
			// 	获取下载文件的总大小
			long totalSize = body.contentLength();
			long downloadedSize = 0;

			int read;
			// 保存文件
			while (!isCanceled && (read = is.read(buffer, 0, bufferSize)) != -1) {
				os.write(buffer, 0, read);
				downloadedSize += read;
				
				//更新进度
				if (callback != null) {
					final int progress = (int) (downloadedSize * 100 / totalSize);
					Async.postOnUiThread(new Runnable() {
						@Override
						public void run() {
							callback.update(progress);
						}
					});
				}

				LogUtil.d(LOG_TAG, "file download: " + downloadedSize + " of " + totalSize);
			}

          // 强制os把数据从内存写入到文件中
			os.flush();

			if (isCanceled) {
				return false;
			}

			//md5校验（确保下载文件未被篡改）
			String md5 = MD5Util.getMd5ForFile(zipFile);
			if (!md5.equals(checksum)) {
				LogUtil.e(LOG_TAG, "md5不匹配");
				return false;
			}

			return true;
		} catch (IOException e) {
			e.printStackTrace();
			return false;
		} finally {
		    // 释放流对象 & buff
			IOUtils.closeQuietly(is);
			IOUtils.closeQuietly(os);
			Pools.releaseBuf(buffer);
		}

	}

   ／**
     * 自定义下载callback
     **/
	public interface DownloadCallback {
		/**
		 * 下载成功
		 */
		void onSucess();

		/**
		 * 下载失败
		 */
		void onFail();

		/**
		 * 被取消
		 */
		void onCanceled();

		/**
		 * 更新下载进度
		 *
		 * @param progress
		 */
		void update(int progress);
	}
}
```

##附加学习点：
* ZipUtil：Zip文件操作
* Pools：  Buffer复用（减少内存抖动）
* IOUtils：org.apache.commons.io.IOUtils（ioStream操作工具类）
* MD5Util: 文件的md5校验（安全性）
* Android-File目录了解和操作！！！
