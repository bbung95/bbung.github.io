---
layout: post
title: "Filter Stream"
date: 2021-03-24 22:43:00 +0900
categories: java
---

# 보조스트림(Filter Stream)

스트림을 데이터의 흐름이라고 말 할 수 있다.  
I/O 는 InputStream과 Reader, OutputStream과 Writer가 기본 입출력 으로 정해져있는데  
만약 위에 클래스로 입출력을 하게되면 많은양의 데이터를 1byte , char 로 하나씩 읽고 쓰게된다.  
결국 많은 시간이 소요되고 효율성이 떨어지게 된다.

그렇기 때문에 자바에선 보조스트림 지원하는데 이것을 더 많이 사용하게된다.  
보조 스트림은 기본 스트림을 확장하는 스트림이고 자체적으로 입출력을 할 수 없다  
쉽게 말해 입력할때 1차 스트림으로 데이터를 받고 보조스트림을 통해 입력을 받게된다.

```java
inputStreamReader in = System.in; // 키보드를 통해 문자열을 입력받음 (1차스트림)
FileReader fr = new FileReader(in); // 1차스트림을 FileReader(보조스트림)로 변환(?)
BufferedReader br = new BufferedReader(fr); FileReader를 BufferReader로 변환(?)
```


### FileReader/Writer, BufferedReader/Writer 예제

```java
package test;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.util.Scanner;

public class IoTest {

	public IoTest() {
	}

	public static void main(String[] args) throws Exception {
		
		Scanner sc = new Scanner(System.in);
		
		BufferedReader reader = null;
		BufferedWriter writer = null;
		File file = null;
		String path = "C:/Users/pc/Desktop/test.txt";
        // 파일의 path를 잡아줌
		try {
				file = new File(path);
                // 파일 객체 생성
				if(!file.exists()) {
					file.createNewFile();
				}
                // 만약 해당 path에 파일이 존재하지 않을 시 파일 생성
			reader = new BufferedReader(new FileReader(file));
			
			String read = null;
			while((read = reader.readLine()) != null) {
				System.out.println(read);
			}
			
			writer = new BufferedWriter(new FileWriter(file, true));
			// true는 append기능 , false는 reset기능 default값은 false이다.

			while(true) {
				String str = sc.nextLine();
				if(str.equals("끝")) {
					return;
				}
				writer.write(str+"\n");
			}

            // writer.flush() 
            // close시 auto-flush
		} catch (Exception e) {
			System.out.println("파일을 읽지 못했습니다.");
		}finally {
			reader.close();
			writer.close();
		}
		
	}
}
```

