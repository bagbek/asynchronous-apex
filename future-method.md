# Future Methods
Future Methods- Asynchronous jobs running in a separate thread when resources are available. Primitive data type support only. Future methods must be **static** methods and can only **return a void type. (Global or Public)**
### Applicability:
- Callouts to external applications or web services
- Perform complex calculations.
- Resolve and Mixed DML Operation errors.
  
**NOTE:** In situations where we need to make a call out to a web service or want to offload simple processing to an asynchronous task. Changing a method from synchronous to asynchronous. Just add the @future annotation to our method. For example, to create a method for performing a web service callout, we could do something like this: @future(callout=true)    static void myFutureMethod(Set<Id> ids)
### Limitations:
- We can’t track execution because no Apex job ID is returned.
- Parameters must be primitive data types, arrays of primitive data types, or collections of primitive data types. Future methods can’t take objects as arguments.
- Cannot invoke a future method from another future method.

### Example:
We cannot controll order of execution when calling couple future methods.
```apex
public class FutureDemo{
    public void invokeFutureCall(){
        futureMethod1();
        futureMethod2();
        futureMethod3();
    }

    @future
    public static void futureMethod1(){
        System.debug('1st Future Method');
    }

     @future
    public static void futureMethod2(){
        System.debug('2nd Future Method');
    }

     @future
    public static void futureMethod3(){
        System.debug('3rd Future Method');
    }
}
```

### Example: Do not Support Non primitive data types
```apex
public class FutureDemo1{
    public void invokeFutureCall(){
        Account accs =[SELECT Id, Name FROM Account LIMIT 10];
        System.debug('Accounts: '+ accs);

        futureMethod(accs);
    }

    @future
    public static void futureMethod(Account inputAcc){
        System.debug(inputAcc);
    }
}
```
We get an Error that: Future methods DO NOT support PARAMETER type of Account.

### Example: If you want to WEB Service Calls or making REST or SOAP Calls to external system:
- You have to add an attribute to the annotation
```apex
public class FutureWebCall{
    public void invokeFutureWeb(){
        futureMethodWeb(accs);
    }

    @future (callout = true)
    public static void futureMethodWeb(){
        System.debug('Web Call out');
    }
}
```