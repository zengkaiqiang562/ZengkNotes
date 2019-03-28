## android.os.AsyncTask<Params, Progress, Result> ##

	public abstract class AsyncTask<Params, Progress, Result>

	@MainThread
    public final AsyncTask<Params, Progress, Result> execute(Params... params) {
        return executeOnExecutor(sDefaultExecutor, params);
    }

	@MainThread
    public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,
            Params... params)

	@MainThread
    protected void onPreExecute() {}

	@WorkerThread
    protected abstract Result doInBackground(Params... params);

	@WorkerThread
    protected final void publishProgress(Progress... values)
		//此方法一般在doInBackground中调用，用于向主线程UI更新进度

	@MainThread
    protected void onProgressUpdate(Progress... values) {}

	@MainThread
    protected void onPostExecute(Result result) {}

	public final boolean cancel(boolean mayInterruptIfRunning)

	@MainThread
    protected void onCancelled(Result result) {
        onCancelled();
    } 

	@MainThread
    protected void onCancelled() {}