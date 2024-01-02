org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor

스프링에서 쓰레드 풀등록

~~~java
@Configuration
public class IOTaskExecutorCOnfig {
	@Bean(“iOTaskExecutor”)
	public TaskExecutor IOTaskExecutor() {
		ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
		executor.setCorePoolSize(18);   // 코어 스레드수를 cpu 코어수와 맞춤
		executor.setQueueCapacity(100);                  //작업큐의 크기
		executor.setThreadNamePrefix(“IOTaskExecutor-“); //생성되는 스레드의 이름 접두사
		executor.initialize();
		return executor;
	}

}

~~~

등록된 빈을 SingleExcutor 대신 사용
~~~java
@Service
public class CommonServiceImpl {
	private final TastExecutor taskExcutor;

	public CommonServiceImpl(@Qualifier(“iOTaskExecutor”) ThreadPoolTaskExecutor taskExecutor) {
		this.taskExecutor = taskExecutor;
	}

	public String asyncProcess(String data){
		CompletableFuture<String> completableFuture = new CompletableFuture<String>();

	taskExecutor.execute(() -> {
		String result = null;

		try{
			result = juminCheckTest(data);
			completableFuture.complete(result);
			
		}catch(Exception e) {
			LOOGER.info(“#### 내부 메소드 실행중 에러 : {} ####“, e.getCause());
		}
	});

	try{
		String result = completableFuture.get(60, TimeUnit.SECONDS);
		return result;
	}catch{
	  // 이하 로직 동일 
	}
	
	}

}


~~~
바꾼 이유
차이점 서술할것 



### 주제




### 참고
* 

