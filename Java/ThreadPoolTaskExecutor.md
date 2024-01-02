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
	private final TaskExecutor taskExcutor;

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
	} catch (InterruptedException e) {
		Thread.currentThread().interrpt();
	} catch (ExcutionException e) {
		LOGGER.info(“######### ExecutionException : {} #######”,e.getCause());
	} catch (TimeoutException e) {
		completableFuture.cancel(true);
		LOGGER.info(“######### Timeout 으로 쓰레드 종료 #########“);
	} 
	return null;

}


~~~


#### Executor vs TaskExecutor 
java.util.concurrent.Executor 를 스프링 프레임워크에서 동일하게 따서 만든 인터페이스가 org.springframework.core.task.TaskExecutor 이다. 실제로 구현 내용을 보면 다음과 같다.

```java
package org.springframework.core.task;  
  
import java.util.concurrent.Executor;  
  
@FunctionalInterface  
public interface TaskExecutor extends Executor {  
    void execute(Runnable var1);  
}

```

스프링 프레임워크에서 기존에 존재하는 Executor를 그대로 따서 TaskExecutor를 만든 이
유는 스프링에서 쓰레드 풀을 사용해야하는 경우 java 라이브러리의  필요성을 추상화하기 위해서 


### 주제




### 참고
* 

