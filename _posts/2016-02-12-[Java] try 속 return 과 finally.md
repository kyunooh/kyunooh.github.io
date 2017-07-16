---
layout: post
title: "[Java] try 속 return 과 finally"
date: 2016-02-12 00:00:00 +0900
categories: Know-how 
---


## SqlSessionFactory를 공부하던중...

오늘 마이바티스([Mybatis](https://github.com/mybatis/mybatis-3))를 다시 공부하던중 이상한것을 발견하게 되었다.

흔하게 사용되는 SqlSessionFactory를 싱글톤으로 만들어서 Session을 열고 닫는 예제였는데, 내용은 아래와 같았다.

(아래 소스의 동작을 이해하시는 분이라면 이미 제가 어디서 멘붕 당했는지 알고 계실겁니다 ㅎㅎ)

    public Student findStudentById(Integer studId){
        logger.debug("Select Student By ID :{}", studId);
        SqlSession sqlSession = MyBatisSqlSessionFactory.openSession();
        try {
            StudentMapper studentMapper = sqlSession.getMapper(StudentMapper.class);
            return studentMapper.findStudentById(studId);
        } finally {
            sqlSession.close();
        }

    }

내가 알기론 메소드에서 return을 만나면 함수가 종료되고 끝인데 그 뒤에 finally가 붙어있는게 아닌가...

저렇게 코드를 작성하면 실행상의 문제가 아니라 컴파일 에러부터 발생할 것 같다라고 생각했는데,

막상 컴파일과 실행 전부 다 잘된다....

## 결론은 try안에 return을 만나도 finally블록안의 내용은 실행됩니다.

너무 궁금해진 나는 결국 따로 코드를 작성해보기로 하였다.

    public class FinallyTest {
        public static void main(String args[]){
            String testString = whatIsReturn();
            System.out.println(testString);
        }

        public static String whatIsReturn(){
            try{
                String returnString = "I'm in try";
                return returnString;
            } finally {
                System.out.println("excuted finally");
            }
        }
    }

내 예상으로는 "I'm in try"만 출력하고 프로그램이 종료되는 것이었지만, 그렇지 않았다.

결과는 아래와 같았다.

![](http://i62.tinypic.com/2vtwlzr.png)

즉 return을 만나도 finally의 구문은 실행된다.

굉장히 많이 사용함에도 불구하고, 이런 동작을 몰랐다는 게 꾀나 충격적이었다.

지금 생각해보면 참 멍청하지만, 이걸 모르기 전까지는 아래와 같이 코드를 작성하였다.

(SI에서 일할때 실제로 아래와 같이 사용해왔던 것 같다...)

    public Student findStudentById(Integer studId){
        SqlSession sqlSession = MyBatisSqlSessionFactory.openSession();
        StudentMapper studentMapper = null;
        try {
            studentMapper = sqlSession.getMapper(StudentMapper.class);
        } finally {
            sqlSession.close();
        }
        return studentMapper.findStudentById(studId);
    }

이 소스를 잘 보다 보면 맨 위에 적힌 소스가 더 효율적으로 잘 짜여져 있고, 안전한 코드임을 확인할수 있다.

추가적인 내용은 GsBOB 님의 블로그를 참조하자 : [http://gogorchg.tistory.com/entry/JAVA-try-catch-finally-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EB%AC%B8%EC%A0%9C%EC%A0%90](http://gogorchg.tistory.com/entry/JAVA-try-catch-finally-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EB%AC%B8%EC%A0%9C%EC%A0%90)