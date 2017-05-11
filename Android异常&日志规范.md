#Android异常处理规范

1. 【强制】不要捕获Java 类库中定义的继承自 RuntimeException 的运行时异常类，如：
IndexOutOfBoundsException / NullPointerException。
	
	说明：这类异常在Java中属于UnChecked Exceptions，不强制要求catch或throws声明，通常是程序本身的不正确使用引起，应该由程序员预检查来规避，保证程序健壮性。

	正例：
	
		if(obj != null) {
			...
		}
	
	反例：

		try { 
			obj.method() 
		} catch(NullPointerException e) {
			…
		}
	参考资料：
	[**Unchecked Exceptions — The Controversy**](https://docs.oracle.com/javase/tutorial/essential/exceptions/runtime.html)
	
2. 【强制】异常不要用来做流程控制，条件控制，因为异常的处理效率比条件分支低。

	说明：在Java异常机制中，当异常出现时，JVM需要执行额外的操作定位处理异常的代码块（利用方法调用栈），这时对性能造成负面影响。因此，不应该使用异常处理机制来控制程序的正常流程。

3. 【强制】对大段代码进行 try-catch，这是不负责任的表现。catch 时请分清稳定代码和非稳
定代码，稳定代码指的是无论如何不会出错的代码。对于非稳定代码的 catch 尽可能进行区分
异常类型，再做对应的异常处理。

	说明：直接利用基类Exception捕获异常，一方面会捕获RuntimeException类型的异常，违反规则1；另一方面不利于对异常进行分类处理。

	正例：

		try {
	    	// 可能抛出多种Checked Exceptions的非稳定代码
	    	···
		} catch (IOException exception1) {
	    	// 仅仅处理IOException类型异常
	    	···
		} catch (SQLException exception2) {
			// 仅仅处理SQLException类型异常
			···
		} catch (OtherException exception3) {
		   // 仅仅处理XX类型异常
		   ···
		}
		// 稳定代码
		···
		
	反例：

		try {
	    	// 可能抛出多种Checked Exceptions的非稳定代码
	    	···
	    	// 稳定代码
	    	···
		} catch (Exception exception) {
	    	// 利用基类Exception捕获所有潜在的异常
	    	···
		}
		
4. 【强制】捕获异常是为了处理它，不要捕获了却什么都不处理而抛弃之，如果不想处理它，请
将该异常抛给它的调用者。最外层的业务使用者，必须处理异常，将其转化为用户可以理解的
内容。
	
	说明：某些情况下，代码调用者需要针对被调用函数可能出现的异常，进行特定处理，如果被调用函数直接捕获异常却不处理，则异常相当于被忽略，会出现意想不到的效果。
	
	正例：
		
		public FileOutputStream(String name) throws FileNotFoundException {
      		this(name != null ? new File(name) : null, false);
    	}
    反例：
		
		public FileOutputStream(String name) {
			try {
				this(name != null ? new File(name) : null, false);
			} catch (FileNotFoundException exception) {
				// do nothing
			}
    	}
    	
5. 【强制】finally 块必须对资源对象、流对象进行关闭，有异常也要做 try-catch或必要的throws声明。

	说明：如果 JDK7及以后版本，可以使用 try-with-resources 方法。try-with-resources保证其内部声明的资源的释放，资源即继承java.lang.AutoCloseable的对象。
	
	正例1:
	
		static String readFirstLineFromFileWithFinallyBlock(String path) throws IOException {
    		BufferedReader br = new BufferedReader(new FileReader(path));
    		try {
        		return br.readLine();
    		} finally {
        		if (br != null) {
        			br.close();
        		}
    		}
		}
	正例2（Java SE 7 and later）：
	
		static String readFirstLineFromFile(String path) throws IOException {
    		try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        		return br.readLine();
    		}
		}
	反例（未及时回收资源）：
			
		static String readFirstLineFromFileWithFinallyBlock(String path) throws IOException {
    		BufferedReader br = new BufferedReader(new FileReader(path));
    		try {
        		return br.readLine();
    		}
		}
	参考资料：[**The try-with-resources Statement**](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)

6. 【推荐】方法的返回值可以为 null，不强制返回空集合，或者空对象等，但必须添加注解android.support.annotation.Nullable，注释充分说明什么情况下会返回 null 值。调用方需要进行 null 判断防止 NPE 问题。
	
	说明：本规约明确防止 NPE 是调用者的责任。即使被调用方法返回空集合或者空对象，对调用
者来说，也并非高枕无忧，必须考虑到远程调用失败，运行时异常等场景返回 null 的情况。

7. 【推荐】防止 NPE，是程序员的基本修养，注意 NPE 产生的场景：

	1） 返回类型为包装数据类型，有可能是 null，返回 int 值时注意判空。
		
		反例：public int f(){ return Integer 对象}，如果为 null，自动解箱抛 NPE。
		
	2） 数据库的查询结果可能为 null。
	
	3） 集合里的元素即使 isNotEmpty，取出的数据元素也可能为 null。
	
	4） 远程调用返回对象，一律要求进行 NPE 判断。
	
	5） 对于 Session 中获取的数据，建议 NPE 检查，避免空指针。
	
	6） 级联调用 obj.getA().getB().getC()；一连串调用，易产生 NPE。
	
8. 【推荐】定义时区分 unchecked / checked 异常，避免直接使用 RuntimeException 抛出，更
不允许抛出 Exception 或者 Throwable，应使用有业务含义的自定义异常（附加必要的错误说明）。

	正例：
	
		// Wzp初始化的异常
		public class WZPInitException extends Exception {
			public WZPInitException() {
				super();
			}
			public WZPInitException(String detailMessage) {
				super(detailMessage);
			}
			public WZPInitException(String detailMessage, Throwable t) {
				super(detailMessage, t);
			}
		}
	
	参考资料：自定义异常的规则，可参考	[**Creating Exception Classes**](https://docs.oracle.com/javase/tutorial/essential/exceptions/creating.html)
		
#Android日志规范
1.	【强制】应用中不推荐直接使用android.util.Log中的API，而应该依赖于自定义框架LogUtil或第三方日志框架，方便进行一些自定义功能。说明：框架应该至少满足以下条件：

	* 限制线上版本的Log信息输出
	
	* 提供统一的日志格式输出，从而有利于从日志中获取有效信息；
	
	* 提供不同Log级别输出日志，便于根据日志的重要程度进行筛选和过滤所需信息
	
   * 记录特定级别（info）以上的日志信息到日志文件中，并提供方法上传日志文件至服务器（用于定位线上bug）

2. 【强制】try catch捕获到异常时，避免直接使用Throwable的成员方法printStackTrace()打印异常信息，应该使用日志框架控制异常信息的输出。

	说明：日志框架应该满足以下两点，对异常信息限制仅调试阶段输出信息，且异常信息记录至日志文件中，方便定位线上bug。
	
	正例：
	
		public static void e(String tag, String msg, Throwable throwable) {
			if (isDebug) {
				Log.e(tag, msg, throwable);
			}
			// 记录异常信息至日志文件
			log2File(Level.SEVERE, tag, msg, throwable);
		}
		
3.  【推荐】使用日志框架时，涉及日志文件写入的日志打印函数需要异步执行，防止占用主线程。

4. 【推荐】输出的 POJO 类必须重写 toString 方法，否则只输出此对象的 hashCode 值（地址值），没参考意义。

5.	【推荐】对于重要的流程性代码块，在可能导致流程结束的各分支处，调用info级别以上的日志输出函数记录导致流程结束的信息至日志文件中，从而方便定位线上bug的原因。

	说明：对于客户端程序来说，线上bug的解决离不开日志文件的查看，因此尽量输出必要的信息至日志文件中。

6. 【推荐】谨慎地记录日志。
	* 线上版本禁止输出debug日志。
	* debug级别：记录仅调试阶段需要查看的信息；
	* info级别：记录定位线上问题时需要查看的数据类型信息；
	* warn级别：记录可能导致错误的信息；
	* error级别：记录导致业务出错的错误信息或异常信息。
	
	说明：客户端的日志文件大小受限，因此有选择地记录必要的日志，才能方便更有效地定位线上bug的原因。

7. 【推荐】使用统一的输出格式记录日志，方便日志的查看和筛选过滤。如：记录日志的产生位置和额外信息

	正例：
	
		private static final String METHOD_LOG_FORMAT = "%s\nclass: %s, method: %s, extra: %s";
		
		public static String makeMethodLog(@NonNull String className, @NonNull String methodName, @NonNull String message, @Nullable String extra) {
			return String.format(METHOD_LOG_FORMAT, message, className, methodName, extra);
		}

8. 【推荐】日志信息中的LOG_TAG一般设置为日志产生的类名，必须使用常量字符串命名，避免使用Class<T>的成员方法getSimpleName()赋值。

	说明：线上版本一般设置混淆，使用后者方法可能产生无意义的字符串，不利于查看和筛选所需日志信息。
	
	正例：
		
		private static final String LOG_TAG = "LogUtil";
		
    反例：
		
		private static final String LOG_TAG = LogUtil.class.getSimpleName();

9.  【推荐】网络访问的信息输出，包括请求的url和具体返回数据。
	