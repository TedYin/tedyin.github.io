---
title: Parcelable Vs Serializable
layout: post
date: 2015-04-03 22:28:00
---
今天说一下对象序列化时的两个接口`Serializable`和`Parcelable`，前者是Java中的老面孔了，大家也都非常熟悉了，后者是Android提供的新面孔，既然Java本身就有序列化的接口为什么Android还要重新造轮子呢？当然Google也不是傻子，肯定是前面那个轮子不能满足他们的要求，所以他们才重新造出了`Parcelable`这个接口。

## Serializable接口的使用
首先来看看`Serializable`如何使用。
{% highlight java %}
public class SerializableClass implements Serializable{
	String name;
	int age;
	float height;
	List<Skill> skillList;

	static class Skill implements Serializable{
		String skillName;
		boolean gotIt;
	}
}
{% endhighlight %}

好了，这样就可以直接将这个类序列化后使用Intent/Bundle进行传递了。使用起来非常简单，只需要实现`Serializable`接口即可。`Serializable`接口是一个标志接口，只要实现了这个接口，你就不需要做任何操作，Java会使用Reflection(反射技术)将类进行序列化和反序列化，非常的简单方便。但是有一个问题就是**效率太低**,在序列化和反序列化过程中会创建许多中间对象，会使得GC多次被调用，从而影响到程序的性能，我们都知道在GC被触发后进行垃圾回收的过程中，线程是被挂起的，APP会处于无响应状态，尽管这个过程持续的时间很短很短只有几十毫秒，但如果多次重复必定会影响到用户体验，一次Google的大牛们就不淡定了，这么差的性能怎么忍得了？但是如何破呢？答案就是`Parcelable`，他们造出了`Parcelable`来解决这个问题。

##Parcelable接口的使用
和上面一样，还是先来看看`Parcelable`接口是如何使用的。
{% highliht java %}
public class ParcelableClass implements Parcelable{
	String name;
	int age;
	float height;
	List<Skill> skillList;

	static final Parcelable.Creator<ParcelableClass> CREATOR 
		= new Parcelable.Creator<ParcelableClass>(){

		@Overried
		ParcelableClass createFromParcel(Parcel in){
			return new ParcelableClass(in);
		}

		@Overried
		ParcelableClass[] newArray(int size){
			return new ParcelableClass[size];
		}
	}

	ParcelableClass(Parcel in){
		this.name = in.readString();
		this.age = in.readInt();
		this.height = in.readFloat();
		this.skillList = new ArrayList<Skill>();
		in.readTypedList(skillList,Skill.CREATOR);
	}

	@Overried
	void writeToParcel(Parcel dest, int flags){
		dest.writeString(this.name);
		dest.writeInt(this.age);
		dest.writeFloat(this.height);
		dest.writeTypedList(this.skillList);
	}

	@Overried
	int describeContents(){
		return 0;
	}

	static class Skill implements Parcelable{
		String skillName;
		boolean gotIt;

		static final Parcelable.Creator<Skill> CREATOR 
			= new Parcelable.Creator<Skill>(){

			@Overried
			Skill createFromParcel(Parcel in){
				return new Skill(in);
			}

			@Overried
			Skill[] newArray(int size){
				return new Skill[size];
			}
		}

		Skill(Parcel in){
			this.skillName = in.readString();
			this.gotIt = in.readBoolean();
		}

		@Overried
		int describeContents(){
			return 0;
		}

		@Overried
		void writeToParcel(Parcel dest, int flags){
			dest.writeString(skillName);
			dest.writeBoolean(gotIt ? 1 : 0);
		}
	}

	static class Skill implements Parcelable{
		String skillName;
		boolean gotIt;
	}
}
{% endhighlight %}
终于写完了，这比刚才使用`Serializable`接口时的代码长了好几倍，代码的可读性也大不如刚刚的代码，但是为了性能上的提升，这么做也是值得的，与性能相比代码多点难点又算得了什么呢？何况这个代码也不是很难，而且是有迹可循的:

1. 每个实现了Parcelable的接口都需要实现一个`Parcelable.Creator`对象。
2. 提供一个以Parcel为参数的构造函数。
3. 实现`describeContents()`方法
4. 实现`writeToParcel(Parcel dest, int flags)`方法。

这样就OK了,下面来看看废了一番周折写出来的序列化方法到底比原生的好了多少，有图有真相！！
![parcelable](http://blog.tedyin.me/images/parcelable_vs_serializable.png)

OMG !这差距，使用`Parcelable`虽然复杂点，但是这性能提升，真是值了！

## 参考
[Parcelable vs Serializable](http://www.developerphil.com/parcelable-vs-serializable/)




