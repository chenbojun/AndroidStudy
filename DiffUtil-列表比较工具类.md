#DiffUtil
##前言

android.support.v7.util包中的工具类，用于比较两个lists，生成一系列更新操作序列（DiffUtil.DiffResult，实现旧列表-》新列表），适应于RecyclerView.Adapter或ListUpdateCallBack。

##简单使用
###DiffUtil.Callback

实现DiffUtil.Callback用于比较列表，通过调用DiffUtil.calculateDiff(Callback cb)，生成DiffUtil.DiffResult。

	/**
     * A Callback class used by DiffUtil while calculating the diff between two lists.
     */
    public abstract static class Callback {
    
        public abstract int getOldListSize();

        public abstract int getNewListSize();
        
        ／**
          * DiffUtil调用该函数判断item的id是否一致
          *／
        public abstract boolean areItemsTheSame(int oldItemPosition, int newItemPosition);

        ／**
          * DiffUtil调用该函数判断item的内容是否一致
          *／
        public abstract boolean areContentsTheSame(int oldItemPosition, int newItemPosition);

        ／**
          * 当areItemsTheSame返回true && areContentsTheSame返回false时，DiffUtil调用该函数用于生成变化的内容                
          * 部分。
          * 如：RecycleView中的应用场景，返回不同的fields，android.support.v7.widget.RecyclerView.ItemAnimator可用于显示正常的动画，刷新部分内容区域.m
          *／
        @Nullable
        public Object getChangePayload(int oldItemPosition, int newItemPosition) {
            return null;
        }
    }
###DiffUtil.DiffResult

该类保存了DiffUtil.calculateDiff(Callback, boolean)的比较结果。可以通过其成员函数dispatchUpdatesTo(ListUpdateCallback)或者 dispatchUpdatesTo(RecyclerView.Adapter)，实现列表的更新。

##注意点

1 数据量及修改量较大的情况，尽量在后台计算然后切换至UI线程更新