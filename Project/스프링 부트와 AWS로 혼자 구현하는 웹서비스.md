
## 프로젝트에  Spring Data Jpa 적용하기 

1. build.gradle  에 의존성 등록하기

```

dependencies {
   implementation('org.springframework.boot:spring-boot-starter-data-jpa')

}

```

2. domain 패키지에 클래스 생성
```java

package com.onthemoment.practice.domain.posts;  
  
  
import com.onthemoment.practice.domain.BaseTimeEntity;  
import lombok.Builder;  
import lombok.Getter;  
import lombok.NoArgsConstructor;  
  
import javax.persistence.*;  
  
@Getter  
@NoArgsConstructor  // 기본 생성자 자동추가
@Entity  
public class Posts extends BaseTimeEntity {  
	/**  
	  @Id : 해당 테이블의 PK 필드를 나타낸다.
	  startegy = GenerationType.IDENTITY : PK를 AUTO_INCREMENT 함(스프링부트2.0)
	 */
    @Id  
    @GeneratedValue(strategy = GenerationType.IDENTITY)  
    private Long id;  
  
    @Column(length = 500, nullable = false)  
    private String title;  
  
    @Column(columnDefinition = "TEXT", nullable = false)  
    private String content;  
  
    private String author;  
  
    @Builder //해당 클래스의 빌터 패턴 클래스를 생성한다.   
    public Posts(String title, String content, String author) {  
        this.title = title;  
        this.content = content;  
        this.author = author;  
  
    }  
	/** @Builder 추가 작성 
	해당 클래스 선언부에 어노테이션을 사용하면 모든 필드가 적용되고 매개변수가 있는 생성자위에 쓴경우 해당매개변수에 대해서만 빌더를 쓸수 있게 해준다.
		   Posts posts = Posts.builder() // 빌더어노테이션으로 생성된 빌더클래스 생성자       .name("seungjin") .age(25) .phone(1234) .build();

	**/


    public void update(String title, String content) {  
        this.title = title;  
        this.content = content;  
    }  
	/**
		Entity 클래스에서는 절대 Setter 메소드를 만들지 않는다. 해당 필드의 값 변경이 필요
		하면 명확히 그 목적과의도를 나타낼수 있는 메소드를 추가 해야한다. 
		

		잘못된 사용 예
		public class order {
			public void setStatus(boolean status){
				this.status = status;
			}
		}

		public void 주문서비스의_취소이벤트(){
			order.setStatus(false);
		}

		---
		올바른 사용 예
		public class Order{
			public void cancelOrder(){
				this.status = false;
			}
		}

        public void 주문서비스의_취소이벤트(){
	        order.cancelOrder();
        }
	**/
}

```

3. Repository 인터페이스 생성 
```java

package com.onthemoment.practice.domain.posts;  
  
import org.springframework.data.jpa.repository.JpaRepository;  
import org.springframework.data.jpa.repository.Query;  
  
import java.util.List;  
  
/* ibatis 나 mybatis 등에서 Dao라고 불리는 DB Layer 접근자입니다.  
jpa에서는 interface로 생성한다.  
JpaRepository<Entity 클래스, PK타입> 를 상속하면 기본적인 CRUD 메소드가 자동으로 생성됨  
단, Entity 와 entity Repository는 함께 위치해야 한다.  
만약 프로젝트 규모가 커져 도메인별로 프로젝트를 분리해야한다면 이때 Entity 클래스와 기본 Repository는 함께 움직여야 하므로  
도메인 패키지에서 함께 관리한다.  
  
SpringDataJpa에서 제공하지 않는 메소드는 쿼리로 작성해도됨.  
*/  
public interface PostsRepository extends JpaRepository<Posts, Long> {  
  
    @Query("SELECT p FROM Posts p ORDER BY p.id DESC")  
    List<Posts> findAllDesc();  
}
```

4. Spring Data JPA 테스트 코드 작성하기 
```java

package com.onthemoment.practice.domain.posts;  
  
import org.junit.After;  
import org.junit.Test;  
import org.junit.runner.RunWith;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.orm.jpa.EntityManagerFactoryBuilder;  
import org.springframework.boot.test.context.SpringBootTest;  
import org.springframework.test.context.junit4.SpringRunner;  
  
import javax.persistence.EntityManager;  
import javax.persistence.EntityManagerFactory;  
import javax.persistence.EntityTransaction;  
import javax.persistence.Persistence;  
import java.time.LocalDate;  
import java.time.LocalDateTime;  
import java.util.List;  
  
import static org.assertj.core.api.Assertions.assertThat;  
  
@RunWith(SpringRunner.class)  
@SpringBootTest  
public class PostsRepositoryTest {  
  
    @Autowired  
    PostsRepository postsRepository;  
  
    @After  
    public void cleanup(){  
        postsRepository.deleteAll();  
  
    }
    /***
	  @After
	  Junit에서 단위테스트가 끝날때마다 수행되는 메소드를 지정 
	  보통은 배포 전 전체 테스트를 수행할때 테스트간 데이터 침범을 막기위해 사용함.
	  여러 테스트가 동시에 수행되면 테스트용 데이터베이스인 h2에 데이터가 그대로 남아있어 다음       테스트 실행시 테스트가 실패할 수 있다.	
	**/
  
    @Test  
    public void boardSaveAndGet(){  
        //given  
        String title = "테스트 게시글";  
        String content = "테스트 본문";  
  
        postsRepository.save(Posts.builder()  
                                    .title(title)  
                                    .content(content)  
                                    .author("jojoldu@gmail.com")  
                                    .build()) ;  
         /***
          save
          테이블 posts 에 insert/update 쿼리를 실행
          id 값이 있다면 update 가 없다면 insert 쿼리가 실행된다.
		 **/					
        //when  
        List<Posts> postsList = postsRepository.findAll();  
  
        //then  
        Posts posts = postsList.get(0);  
        assertThat(posts.getTitle()).isEqualTo(title);  
        assertThat(posts.getContent()).isEqualTo(content);  
  
        /**  
         * 객체를 단순히 생성만 했을떄는 안생김  
         */  
        Posts post2 = Posts.builder()  
                .title("hey")  
                .content("uhuhuhu")  
                .author("realreal")  
                .build();  
  
        System.out.println("##### post2.getCreatedDate() : "+post2.getCreatedDate());  
        System.out.println("##### post2.getModifiedDate() : "+post2.getModifiedDate());  
        System.out.println("##### post2.getAuthor() : "+post2.getAuthor());  
  
  
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpaTest");  
        EntityManager em = emf.createEntityManager();  
        EntityTransaction transaction = em.getTransaction();  
  
        // 엔티티 매니저는 데이터 변경 시 트랜잭션을 시작해야 한다.  
        transaction.begin();    // 트랜잭션 시작  
  
        em.persist(post2);  
        System.out.println("##### 영속상태 진입 후  ");  
        System.out.println("##### post2.getCreatedDate() : "+post2.getCreatedDate());  
        System.out.println("##### post2.getModifiedDate() : "+post2.getModifiedDate());  
        System.out.println("##### post2.getAuthor() : "+post2.getAuthor());  
         // 여기까지 INSERT SQL을 데이터베이스에 보내지 않는다.  
  
        em.detach(post2);  
        System.out.println("##### 준영속 상태로 변경 후   ");  
        System.out.println("##### post2.getCreatedDate() : "+post2.getCreatedDate());  
        System.out.println("##### post2.getModifiedDate() : "+post2.getModifiedDate());  
        System.out.println("##### post2.getAuthor() : "+post2.getAuthor());  
  
         // 커밋하는 순간 데이터베이스에 INSERT SQL을 보낸다.  
        transaction.commit();   // 트랜잭션 커밋  
  
        System.out.println("##### 커밋이후  "+post2.getCreatedDate());  
        System.out.println("##### post2.getCreatedDate() : "+post2.getCreatedDate());  
        System.out.println("##### post2.getModifiedDate() : "+post2.getModifiedDate());  
        System.out.println("##### post2.getAuthor() : "+post2.getAuthor());  
  
    }  
  
    @Test  
    public void BaseTimeEntity_register() {  
        //given  
        LocalDateTime now = LocalDateTime.of(2019,6,4,0,0,0);  
        postsRepository.save(Posts.builder()  
                .title("title")  
                .content("content")  
                .author("author")  
                .build());  
  
        //when  
        List<Posts> postsList = postsRepository.findAll();  
  
        //then  
        Posts posts = postsList.get(0);  
  
        System.out.println(">>>>>>>>> createDate="+posts.getCreatedDate()+", modifiedDate="+posts.getModifiedDate());  
  
        assertThat(posts.getCreatedDate()).isAfter(now);  
        assertThat(posts.getModifiedDate()).isAfter(now);  
  
    }  
}

```

