# spring-mvc-basic

---

### HTTP 메시지 컨버터

````java
public interface HttpMessageConverter<T> {
	boolean canRead(Class<?> clazz, @Nullable MediaType mediaType);
	boolean canWrite(Class<?> clazz, @Nullable MediaType mediaType);

	List<MediaType> getSupportedMediaTypes();
    
	default List<MediaType> getSupportedMediaTypes(Class<?> clazz) {
		return (canRead(clazz, null) || canWrite(clazz, null) ?
				getSupportedMediaTypes() : Collections.emptyList());
	}
    
	T read(Class<? extends T> clazz, HttpInputMessage inputMessage) 
            throws IOException, HttpMessageNotReadableException;

	void write(T t, @Nullable MediaType contentType, HttpOutputMessage outputMessage) 
            throws IOException, HttpMessageNotWritableException;

}

````

- HTTP 메시지 컨버터는 HTTP 요청과 응답 두 곳에서 모두 사용된다.
- canRead(), canWrite()
  - 메시지 컨버터가 해당 클래스 혹은 미디어타입을 지원하는지 체크한다.
- read(), write() 
  - 메시지 컨버터를 통해서 메시지를 읽고 쓰는 기능을 한다.
  
<br>

#### 스프링 부트의 기본 메시지 컨버터

스프링 부트는 다양한 메시지 컨버터를 제공하는데, 대상 클래스 타입과 미디어 타입을 체크해서 사용여부를 결정한다. 만약 조건에 맞지 않다면 다음 메시지 컨버터로 우선순위가 넘어간다.

> xxxHttpMessageConverter.canRead() = false => next converter
> xxxHttpMessageConverter.canWrite() = false => next converter

0. ByteArrayHttpMessageConverter 
   - class type : byte[]
   - media type : \*/*
   - 요청 예) @RequestBody byte[] data
   - 응답 예) @ResponseBody return byte[] 
   - 쓰기 미디어 타입 : application/octet-stream 
1. StringHttpMessageConverter
   - class type : String
   - media type : \*/*
   - 요청 예) @RequestBody String data
   - 응답 예) @ResponseBody return "ok"
   - 쓰기 미디어 타입 : text/plain
2. MappingJackson2HttpMessageConverter
   - class type : object or HashMap
   - media type : application/json
   - 요청 예) @RequestBody HelloData data
   - 응답 예) @ResponseBody return new HelloData()
   - 쓰기 미디어 타입 : application/json  

... 등등


---