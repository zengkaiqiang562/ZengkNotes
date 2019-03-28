## Binder.java （implements IBinder）##

	public class Binder implements IBinder {
		
		private String mDescriptor;
		private IInterface mOwner;
	
		public void attachInterface(IInterface owner, String descriptor) {
	        mOwner = owner;
	        mDescriptor = descriptor;
	    }
	
		public String getInterfaceDescriptor() {
	        return mDescriptor;
	    }

		public boolean pingBinder() {
	        return true;
	    }

	    public boolean isBinderAlive() {
	        return true;
	    }

		public IInterface queryLocalInterface(String descriptor) {
	        if (mDescriptor.equals(descriptor)) {
	            return mOwner;
	        }
	        return null;
	    }

		public void linkToDeath(DeathRecipient recipient, int flags) {
	    }
	
	    public boolean unlinkToDeath(DeathRecipient recipient, int flags) {
	        return true;
	    }

		public final boolean transact(int code, Parcel data, Parcel reply,
            int flags) throws RemoteException {
	
	        if (data != null) {
	            data.setDataPosition(0);
	        }
	        boolean r = onTransact(code, data, reply, flags);
	        if (reply != null) {
	            reply.setDataPosition(0);
	        }
	        return r;
	    }

		//返回false表示客户端请求失败
		protected boolean onTransact(int code, Parcel data, Parcel reply,
            int flags) throws RemoteException {
	        if (code == INTERFACE_TRANSACTION) {
	            reply.writeString(getInterfaceDescriptor());
	            return true;
	        } else if (code == DUMP_TRANSACTION) {
	           ...
	                try {
	                    dump(fd.getFileDescriptor(), args);
	                } finally {
	                    IoUtils.closeQuietly(fd);
	                }
	            ...
	            return true;
	        } else if (code == SHELL_COMMAND_TRANSACTION) {
	            ...
	            try {
	                if (out != null) {
	                    shellCommand(in != null ? in.getFileDescriptor() : null,
	                            out.getFileDescriptor(),
	                            err != null ? err.getFileDescriptor() : out.getFileDescriptor(),
	                            args, shellCallback, resultReceiver);
	                }
	            } finally {
	               ...
	            }
	            return true;
	        }
	        return false;
	    }

	}

	final class BinderProxy implements IBinder {

	}
	