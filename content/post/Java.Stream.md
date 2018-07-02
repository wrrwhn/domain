---
title: "Java.Stream"
date: "2018-04-25"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


# 分类
## InputStream
- PipedInputStream
- ObjectInputStream
- FilterInputStream
	- DataInputStream
	- PushbackInputstream
	- BufferedInputStream
- FileInputStream
- SequenceInputStream
- ByteArrayInputStream

## OutputStream
- PipedOutputStream
- ObjectOutputStream
- FilterOutputStream
	- DataOutputStream
	- BufferedOutputStream
	- PrintStream
- FileOutputStream
- ByteArrayOutputStream

## Reader
- StringReader
- PipedReader
- CharArrayReader
- InputStreamReader
	- FileReader
- FilterReader
	- PushbackReader
- BufferedReader
	- LineNumberReader

## Writer
- StringWriter
- PipedWriter
- ChatArrayWriter
- FilterWriter
- OutputStreamWriter
	- FileWriter
- BufferedWriter
- PrintWriter


# 明细
## Interface
### OutputStream
- methods
	- write(int b)
	- write(byte b[]) 
	- write(byte b[], int off, int len) 
	- flush() 
	- close()

### InputStream
- methods
	- read()
	- read(byte b[])
	- read(byte b[], int off, int len)
	- skip(long n)
	- available()
	- close()
	- synchronized void mark(int readlimit)
	- synchronized void reset()

### Reader
- params
	- **Object lock**
- methods
	- read([char buf[], [int off, int len]])
	- skip(long n)
	- ready()
	- mark(int readAheadLimit)
	- reset()
	- close()

### Writer
- params
	- char[] writeBuffer
	- **Object lock**
- methods
	- write(int|char[]|string)
	- Writer append(CharSequence csq[, int start, int end])
	- flush()
	- close()

## Stream
### Piped*Stream

- 用法
	- **多线程**时通过**管道**进行**线程间的通讯**
- 设计
	- PipedOutputStream
		- methods
			- connect(PipedInputStream snk)
			- write(int b)
			- write(byte b[], int off, int len)
	- PipedInputStream
		- params
			- DEFAULT_PIPE_SIZE = 1024
			- Thread readSide, writeSide;
		- methods
			- **connect(PipedOutputStream src)**
			- receive(int b)
			- receive(byte b[], int off, int len)
			- read()
			- read(byte b[], int off, int len)

### Object*Stream

- 用法
	- 通过文件永久地**存储住对象**（需支持 Serializable 接口）
- 设计
	- ObjectOutputStream
		- params
			- BlockDataOutputStream bout
			- HandleTable handles
			- ReplaceTable subs
		- methods
			- ObjectOutputStream(OutputStream out)
			- write[Object|Boolean|Short|Char|Int|Long|Double|Bytes|Chars]
	- ObjectInputStream
		- params
			- BlockDataInputStream bin
			- HandleTable handles
		- methods
			- ObjectInputStream(InputStream in)
			- read[Object|Boolean|Short|Char|Int|Long|Double|Bytes|Chars]

### Filter*Stream

- 用法
	- 包含其它流，并作为基础的数据源
- 设计
	- BufferedInputStream
		- methods
			- synchronized read()
				- fill()
					- 所有数据被读取情况下，同步变换以填充更多的数据
	- DataInputStream
		- methods
			- DataInputStream(InputStream in)
			- **readFully(byte b[])**
			- read[Boolean|Short|Char|Int|Long|Float|Double|Bytes|Line|UTF]
	- PushbackInputstream
		- params
			 - **byte[] buf**
			 - int pos
			 	- 缓冲池，用于存放 unread 数据
		- methods
			- PushbackInputStream(InputStream in[, int size= 1]) 
			- int read([byte[] b, int off, int len])
				- **优先读取 buf 中数据**
				- 读取完成后，再行读取流中的数据
			- unread(int b)
			- unread(byte[] b[, int off, int len])
	- BufferedOutputStream
		- methods
			- BufferedOutputStream(OutputStream out)
			- synchronized void write(int b)

	- DataOutputStream
		- methods
			- DataOutputStream(OutputStream out)
			- synchronized void write(int b)
			- write[Boolean|Short|Char|Int|Long|Float|Double|Bytes|Line|UTF]
			- writeLong(long v)
				- writeBuffer[0] = (byte)(v >>> 56);
					- **8字节，对应位转换**
	- PrintStream
		- characteristic
			- 封装其它流以提供便利输出大量数据的能力
			- 不抛出 `IOException`，而以内部标志们以记录
			- 自动 flush 数据
		- params
			- **boolean autoFlush**
			- trouble = false
		- methods
			- PrintStream(OutputStream out[, boolean autoFlush, String encoding])
			- checkError()
			- print[ln](boolean|char|int|long|float|double|char|String|Object)
			- printf(String format, Object ... args)
			- append(CharSequence csq[, int start, int end])

### File*Stream

- 用法
	- 以流的形式进行文件或文件描述符的操作
		- 支持自动创建文件
		- 文件追加形式
	- 文件已打开情况下，无法进行写操作
	- 读取时推荐用于**读取图片等形式数据**，若读取文本，推荐使用 **FileReader**
- 设计
	- FileOutputStream
		- params
			- FileDescriptor fd
			- FileChannel channel
			- Object closeLock
		- methods
			- FileOutputStream(File|String|FileDescriptor[,append=false])
			- write(int b[, boolean append])
			- writeBytes(byte b[], int off, int len, boolean append)
	- FileInputStream
		- params
			- FileDescriptor fd
			- FileChannel channel
			- Object closeLock
		- methods
			- FileInputStream(File|String|FileDescriptor)
			- read([byte[],[int off, int len])
			- close()
				- synchronized (closeLock) {
					- channel.close();
					- fd.closeAll(new Closeable() {

### ByteArray*Stream

- 用法
	- 以流的方式，字节数据的后台存储形式进行数据的存取
	- 关闭流时不需要处理 `IOException` 异常
- 设计
	- ByteArrayInputStream
		- params
			- **byte buf[]**
			- int pos
			- int count
		- methods
			- ByteArrayInputStream(byte buf[][, int offset, int length])
			- read([byte b[], int off, int len])
	- ByteArrayOutputStream
		- params
			- byte buf[]
			- int count
			- **MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8**
		- methods
			- ByteArrayOutputStream([int size=32])
			- write(int b)
			- write(byte b[], int off, int len)
			- **synchronized** void writeTo(OutputStream out)
			- **synchronized** byte toByteArray()**[]**
			- **synchronized** String toString()

### SequenceInputStream
- 用法
	- 按**顺序读取**多个初始流，直至最后一个流读取完成
- 设计
	- methods
		- SequenceInputStream(**Vector.elements()**)


## *er
### String*er
- 用法
	- 字符流，用于处理文本
- 设计
	- StringReader
		- methods
			- StringReader(String s)
			- read()
			- read(char cbuf[], int off, int len)
	- StringWriter
		- params
			- StringBuffer buf
		- methods
			- StringWriter([int initialSize])
			- toString()
			- getBuffer()

### Pipe*er
- 参见 Stream.Piped

### CharArray*er
- 参见 Steam.CharArray


### Buffered*er
- 用法
	- 更有效率地由字符串、数组和行记录中读取文本
- 设计
	- BufferedReader
		- params
			- Reader in
			- char cb[]
			- int defaultCharBufferSize = 8192
		- methods
			- BufferedReader(Reader in[, int sz])
			- read()
			- String **readLine()**
			- Stream<String> **lines()**
			- ready()

	- LineNumberReader
		- params
			- int lineNumber = 0
		- methods
			- LineNumberReader(Reader in[, int sz])
			- setLineNumber(int lineNumber)
			- **getLineNumber()**
			- readLine()

	- BufferedWriter
		- params
			- int defaultCharBufferSize = 8192
		- methods
			- BufferedWriter(Writer out[, int sz])
			- write(String s, int off, int len)
				- cb[nextChar++]= (char)c
			- newLine()
			- flush()
				- out.write

### InputStreamReader
- 用法
	- 将字节流转换为字符流的桥梁
- 设计
	- params
		- StreamDecoder sd
	- methods
		- InputStreamReader(InputStream in[, **String charsetName|Charset cs|CharsetDecoder dec**])
			- sd = StreamDecoder.forInputStreamReader(in, this, (String)null);


### OutputStreamWriter
- 用法
	- 将字节流转换为字符流的桥梁
- 设计
	- params
		- StreamDecoder sd
	- methods
		- OutputStreamWriter(OutputStream out[, **String charsetName|Charset cs|CharsetDecoder dec])
			- se = StreamEncoder.forOutputStreamWriter(out, this, (String)null);

### FileReader
- 参见 Stream.FileInput

### PrintWriter
- 用法
	- 格式化地将对象格式化流输出至文本输出流
	- 仅适用于未编码的字节流
- 设计
	- params
		- boolean autoFlush
		Formatter formatter
		Writer out
	- methods
		- PrintWriter(String丨File [, String csn])
		- PrintWriter(Writer丨OutputStream [, boolean autoFlush])
		- `print[ln](boolean丨char丨int丨long丨float丨double丨char[]丨String|Object)`
		- PrintWriter printf(String format, Object ... args)
		- PrintWriter append(CharSequence csq[, int start, int end])
		- newLine()

### Filter*er 
- 设计
	- FilterReader
		- Abstract class for reading filtered character streams.
	- PushbackReader
		- extends FilterReader
		- 参考 Stream.Pushback
	- FilterWriter
		- Abstract class for writing filtered character stream
	- ProxyWriter
		- extends FilterWriter 