# 对象序列化 #
## java.io.ObjectOutputStream ##

	public class ObjectOutputStream
    	extends OutputStream implements ObjectOutput, ObjectStreamConstants

	public ObjectOutputStream(OutputStream out) throws IOException

	public final void writeObject(Object obj) throws IOException

	public void writeBoolean(boolean val) throws IOException
	public void writeByte(int val) throws IOException
	public void writeChar(int val)  throws IOException
	public void writeShort(int val)  throws IOException
	public void writeInt(int val)  throws IOException 
	public void writeLong(long val)  throws IOException
	public void writeFloat(float val) throws IOException
	public void writeDouble(double val) throws IOException


## java.io.ObjectInputStream ##

	public class ObjectInputStream
    	extends InputStream implements ObjectInput, ObjectStreamConstants

	public ObjectInputStream(InputStream in) throws IOException

	public final Object readObject() throws IOException, ClassNotFoundException

	public boolean readBoolean() throws IOException
	public byte readByte() throws IOException
	public char readChar()  throws IOException
	public short readShort()  throws IOException
	public int readInt()  throws IOException
	public long readLong()  throws IOException
	public float readFloat() throws IOException
	public double readDouble() throws IOException

## android.os.Parcelable ##

	public interface Parcelable

	public @ContentsFlags int describeContents();
	
	public void writeToParcel(Parcel dest, @WriteFlags int flags);

### android.os.Parcelable.Creator<T> ###

	public interface Creator<T>

	public T createFromParcel(Parcel source);

	public T[] newArray(int size);

### android.os.Parcelable.ClassLoaderCreator<T> ###

	public interface ClassLoaderCreator<T> extends Creator<T>

	public T createFromParcel(Parcel source, ClassLoader loader);

## android.os.Parcel ##

	public final class Parcel

	public final void writeParcelable(Parcelable p, int parcelableFlags)

	public final void writeInt(int val)
	public final void writeLong(long val)
	public final void writeFloat(float val)
	public final void writeDouble(double val)
	public final void writeString(String val)

	public final void writeByteArray(byte[] b)
	public final void writeIntArray(int[] val)
	public final void writeLongArray(long[] val)
	public final void writeFloatArray(float[] val)
	public final void writeDoubleArray(double[] val)
	public final void writeStringArray(String[] val)
	public final void writeArray(Object[] val)
	
	public final void writeList(List val)
	public final void writeStringList(List<String> val)

	public final void writeMap(Map val) {
        writeMapInternal((Map<String, Object>) val);
    }

	public final <T extends Parcelable> void writeTypedList(List<T> val) {
        if (val == null) {
            writeInt(-1);
            return;
        }
        int N = val.size();
        int i=0;
        writeInt(N);
        while (i < N) {
            T item = val.get(i);
            if (item != null) {
                writeInt(1);
                item.writeToParcel(this, 0);
            } else {
                writeInt(0);
            }
            i++;
        }
    }

	public final <T> ArrayList<T> createTypedArrayList(Parcelable.Creator<T> c) {
        int N = readInt();
        if (N < 0) {
            return null;
        }
        ArrayList<T> l = new ArrayList<T>(N);
        while (N > 0) {
            if (readInt() != 0) {
                l.add(c.createFromParcel(this));
            } else {
                l.add(null);
            }
            N--;
        }
        return l;
    }
	
	public final void writeStrongBinder(IBinder val)

	public final <T extends Parcelable> T readParcelable(ClassLoader loader)

	public final int readInt()
	public final long readLong()
	public final float readFloat()
	public final double readDouble()
	public final String readString()

	public final void readIntArray(int[] val)
	...


## android.os.Binder ##

	public class Binder implements IBinder

### android.os.BinderProxy ###

	final class BinderProxy implements IBinder

## android.os.IBinder ##

	public interface IBinder

	int FIRST_CALL_TRANSACTION  = 0x00000001;

	public String getInterfaceDescriptor() throws RemoteException;

	public boolean pingBinder();

	public boolean isBinderAlive();

	public IInterface queryLocalInterface(String descriptor);

	public void dump(FileDescriptor fd, String[] args) throws RemoteException;
	public void dumpAsync(FileDescriptor fd, String[] args) throws RemoteException;

	public void shellCommand(FileDescriptor in, FileDescriptor out, FileDescriptor err,
            String[] args, ShellCallback shellCallback,
            ResultReceiver resultReceiver) throws RemoteException;

	public boolean transact(int code, Parcel data, Parcel reply, int flags)
        throws RemoteException;

	public void linkToDeath(DeathRecipient recipient, int flags)
            throws RemoteException;

	public boolean unlinkToDeath(DeathRecipient recipient, int flags);

### android.os.IBinder.DeathRecipient ###

	public interface DeathRecipient

	public void binderDied();

## android.os.IInterface ##

	public interface IInterface

	public IBinder asBinder();

