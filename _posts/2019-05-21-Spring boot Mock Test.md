---
title: Spring - Mock을 활용한 테스트
layout: post
description: ""
headline: null
modified: '2019-05-11'
category: Spring
tags: [Spring, Mock, Test]
imagefeature: null
mathjax: null
chart: null
comments: true
featured: true
lastmod : 2019-05-21 12:00:00
sitemap :  
   changefreq : daily
   priority : 1.0
---

# Mock을 활용한 테스트
  
<br />
  
```java
@Repository
public class BookRepository {

	public Book save(Book book) {
		return null;
	}
}
```

```java
@Service
public class Book Service {

	private BookRepository bookRepository;
	
	public BookService(BookRepository bookRepository) {
	this.bookRepository = bookRepository;
	}
}
```

<br />

### 위와 같이 의존성이 있는 객체에 대한 구현이 되지 않았을 때 단위 테스트를 진행하는 방법은 아래와 같다. 

<br />  

```java
@RunWith(SpringRunner.class)
public class BookServiceTest {

	@Mock
	BookRepository bookRepository;
	
	@Test
	public void save() {
	
		Book book = new Book();

		//bookRepository.save(book)를 호출할 때 book을 리턴해줌
		when(bookRepository.save(book)).thenReturn(book);
		BookService bookService = new BookService(bookRepository);
		
		Book result = bookService.save(book);
		
		assertThat(Book.getCreated()).isNotNull();
		assertThat(book.getBookStatus()).isEqualTo(BookStatus.DRAFT);
		assertThat(result).isNotNull();
	}

}
```

<br />

### Project Repository 

- https://github.com/Seongmun-Hong/SpringStudy

<br />

### Reference

- https://www.inflearn.com/course/spring-framework_core