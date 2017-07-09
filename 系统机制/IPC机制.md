# IPC机制

## 一、基础概念
`IPC` 是 `Inter-Process Communication` 的缩写，含义为进程间通信或者跨进程通信，即两个进程之间进行数据交换的过程。

### （1）Serializable接口

`Serializable` 是 `Java` 所提供的一个序列化接口，它是一个空接口，未兑现提供标准的序列化和反序列化操作。实现序列化相当简单，只需要在类的生命中指定一个类似下面的标识即可自动实现默认的序列化过程。示例代码：

    public class User implements Serializable {
		
	    private static final long serialVersionUID = 1L;
	
	    public int userId;
	    public String userName;
	    public boolean isMale;
	
	    public User(int id, String name, boolean isMale) {
	        this.userId = id;
	        this.userName = name;
	        this.isMale = isMale;
	    }
	
	    @Override
	    public String toString() {
	        return "User:[userId = " + userId + " ; "
	                + "userName = " + userName + " ; "
	                + "isMale = " + isMale + "]";
	    }
    }

而序列化的过程，只需要采用 `ObjectOutputStream` 和 `ObjectInputStream` 即可轻松实现。示例代码：


	
	final String allPath = getContext().getApplicationContext().getFilesDir().getAbsolutePath() + "/cache.txt";
    final File file = new File(allPath);

	//序列化过程
    User user = new User(0, "C.C.", true);
    try {
        ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream(file));
        out.writeObject(user);
        out.close();
    } catch (IOException e) {
        e.printStackTrace();
    }

	//反序列化过程
	try {
        ObjectInputStream in = new ObjectInputStream(new FileInputStream(file));
        User newUser = (User) in.readObject();
        Toast.makeText(getContext(), newUser.toString(), Toast.LENGTH_LONG).show();
        in.close();
    } catch (IOException e) {
        e.printStackTrace();
    } catch (ClassNotFoundException e) {
        e.printStackTrace();
    }

在 `User` 的类中有一个常量 `serialVersionUID` 这个值是帮助我们实现序列化的，`serialVersionUID` 的工作机制是这样的：序列化的时候系统会把当前的类的 `serialVersionUID` 写入序列化的文件中（也可能是其他中介），当饭序列化的时候系统会去检测文件中的 `serialVersionUID` ，看是否和当前的 `serialVersionUID` 一直，如果一致就说明序列化的类的版本和当前类的版本是相同的，此时可以成功反序列化；否则就说明当前类和序列化相比发生了某些改变，比如成员变量的数量、类型可能发生了改变，这是是无法发序列化的，因此会报 `java.io.ImvalidClassException` 异常。

一般来说，我们需要手动制定 `serialVersionUID` 的值，比如 1L；这样序列化和反序列化时两者的 `serialVersionUID` 是相同，避免了反序列化时失败。

> 以下两点需要提出，首先**静态成员变量属于类不属于对象**，所以不会参与序列化过程；其次**用transient关键字标记的成员变量不参与序列化过程**。

另外，系统默认的序列化和反序列化的过程也是可以改变的，通过重写系统默认的序列化和反序列化过程，大部分情况我们不需要重写这两个方法。

	@Override
    public void writeObjectOverride(Object obj) throws IOException {
        super.writeObjectOverride(obj);
    }

	@Override
    public Object readObjectOverride() throws IOException, ClassNotFoundException {
        return super.readObjectOverride();
    }

### （2）Parcelable接口

`Parcalable` 也是一个接口，只要实现这个接口，这个累的对象就可以实现序列化并可以通过 `Intent` 和 `Binder` 传递。

	public class User implements Parcelable {
	    public int userId;
	    public String userName;
	    public boolean isMale;
	    /**可序列化对象*/
	    public Book book;
	
	    public User(int userId, String userName, boolean isMale) {
	        this.userId = userId;
	        this.userName = userName;
	        this.isMale = isMale;
	    }
	
	    protected User(Parcel in) {
	        userId = in.readInt();
	        userName = in.readString();
	        isMale = in.readInt() ==1;
	        book = in.readParcelable(Thread.currentThread().getContextClassLoader());
	    }
	
	    public static final Creator<User> CREATOR = new Creator<User>() {
	        @Override
	        public User createFromParcel(Parcel in) {
	            return new User(in);
	        }
	
	        @Override
	        public User[] newArray(int size) {
	            return new User[size];
	        }
	    };
	
	    @Override
	    public int describeContents() {
	        return 0;
	    }
	
	    @Override
	    public void writeToParcel(Parcel parcel, int i) {
	        parcel.writeInt(userId);
	        parcel.writeString(userName);
	        parcel.writeInt(isMale ? 1 : 0);
	        parcel.writeParcelable(book, 0);
	    }
	}

`Parcel` 内部包装率可序列化的数据，可以在 `Binder` 中自由传输。从上面代码可以看出，在序列化的过程中需要实现的功能有序列化、反序列化和内容描述。

- 序列化功能由 `writeToParcel` 方法实现，通过 `Pracel` 的一系列 `write` 方法来完成
- 反序列化功能由 `CREATOR` 来完成，内部创建序列化对象和数组，并通过 `Parcel` 的一系列 `read` 方法来完成反序列化
- 内容描述功能由 `describeContents` 方法完成，几乎所有情况返回值都是 0，仅当对象中存在的文件描述符时，此方法返回 1

> `User` 中有一个 `book` 对象， `book` 也是一个可序列化对象，所以他的反序列化过程需要传递当前县城的上下文类加载器。

`Parcel`方法说明

| 方法                                                 | 功能                              | 标记位                   |
| -------------------------------------------- |-------------------------------| -----------------------|
| createFromParcel(Parcel in)           |从序列化后的对象中创建原始对象| 无 |
| newArray(int size)                          |创建制定长度的原始对象数组| 无 |
| User(Parcel in)                                |从序列化后的对象中创建原始对象| 无 |
| writeToParcel(Parcel parcel, int i) |将当前对象写入序列化结构中，其中`flags`标识有两种值：0 或 1 。为1时标识当前对象需要作为返回值，不能立即释放资源，几乎所有情况都为0.| PARCELABLE_WRITE_RETURN_VALUE|
|describeContents                              |返回当前对象的内容描述。如果有文件描述符，返回1，否则返回0.几乎所有情况都返回0.| CONTENTS_FILE_DESCRIPTOR|

> - `Serializable` 和 `Parcelable` 都能实现序列化并且都可用于 `Intent` 间的数据传递
> - `Serializable` 是 `Java`中的序列化接口，使用简单但开销大，序列化和反序列化过程需要大量的`I/O`操作，建议序列化到存储设备中或者将对象序列化后通过网络传输
> - `Parcelable` 是 Android中国i不过的序列化方式，效率更高，缺点就是使用起来稍微麻烦点，建议用在内存序列化上

### （3）Binder
