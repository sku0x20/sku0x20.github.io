---
layout: post
title:  "java intersection type"
date: 2022-10-30
categories: java
upload-date: 2023-01-29
---

```java
public class MyClass {
    public static void main(String args[]) {
      Test t1 = new TestImpl1();
      testInter(t1);
      
      TestImpl2 t2 = new TestImpl2();
      testInter2(t2);
      
      testGen(t2);
      
      Takes takes = new Takes(t2);
      takes.print();
    
    //   usingBoth(t2); won't work
    
    var both2 = (Test & ElseP) t2;
    
    System.out.println("both2 is" + both2.getClass());
    // won't work
    bothClazz(both2);
    }
    
    static <T extends Test & ElseP> void testGen(T t){
        t.print();
        t.elsePrint();
    }
    
    static void testInter(Test t){
        t.print();
    }
    
    static <T extends Test> void testInter2(T t){
        t.print();
    }
    
    static interface Test{
        void print();
    }
    
    static interface ElseP{
        void elsePrint();
    }
    
    static class TestImpl1 implements Test{
        @Override
        public void print(){
            System.out.println("testimpl-1");
        }
    }
    
    static class TestImpl2 implements Test, ElseP{ //, Both
        @Override
        public void print(){
            System.out.println("testimpl-2");
        }
        
        @Override
        public void elsePrint(){
            System.out.println("testimpl-2-else");
        }
    }
    
    static class Takes<T extends Test & ElseP>{
        T t;
        Takes(T t){
            this.t = t;
        }
        
        void print(){
            t.print();
            t.elsePrint();
        }
    }
    
    interface BothInterface extends Test, ElseP{
        
    }
    
    static class BothClass implements Test, ElseP{
    }

    static void usingBoth(BothInterface b){
        b.print();
        b.elsePrint();
    }
    
    static void bothClazz(BothClass c){
        c.print();
        c.elsePrint();
    }
}
```