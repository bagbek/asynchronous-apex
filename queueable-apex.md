# Queueable Apex
- Asynchronous process that can be queued for sequential processing
- System.Queueable
- Supports Primitive & Non-Primitive types as well
- Generally used for Job Chaining means it respects order of execution

### Syntax
```apex
public class SomeClass implements Queueable {
    public void execute (QueueableContext context) {
    // awesome code here
    }
}
```
### Example:1
```apex
public class QueueableDemo implements System.Queueable {
    public void execute(System.Queueable context){
        System.debug('Inside the execute method of Queueable Job');

        List<Account> listAcc = new List<Account>([SELECT Id, Name FROM Account LIMIT 50]);
        System.debug('List of Accounts: '+ listAcc);
    }
}
```
### To call the method
```apex
QueueableDemo qj = new QueueableDemo();
Id jobId = System.enqueueJob(qj);
System.debug('Job Id: ' + jobId);

//or Id jobId = System.enqueueJob(new QueueableDemo());
```

### Example:2
```apex
public class AccountQueueable implements Queueable{
    public List<Account> accList;
    public AccountQueueable(List<Account>accs){
        this.accList = accs;
    }

    public void execute(QueueableContext context) {
        for(Account a : accList){
            a.Description = 'Queueable Apex';
        }
        update accList;
    }
}
```
### To call the method
```apex
List<Account> accList = [SELECT Id, Name, Description FROM Account];
Id jobId = System.enqueueJob(new AccountQueueable(accList));
System.debug('jobId of Queueable Apex: '+ jobId);
```

### Example: Queueable Apex chaining
```apex
public class MyQueueable implements Queueable {
    public void execute(QueueableContext context) {
        System.debug('First Queueable');
        
        // Chain another Queueable
        System.enqueueJob(new MySecondQueueable());
    }
}

public class MySecondQueueable implements Queueable {
    public void execute(QueueableContext context) {
        System.debug('Second Queueable');
    }
}
```
- In the above code, MyQueueable is the first Queueable that will execute. Inside its execute method, it will print out a debug statement and then chain another Queueable, MySecondQueueable, using the System.enqueueJob method.
- MySecondQueueable is the second Queueable that will execute. Inside its execute method, it will print out a debug statement.
- When you enqueue an instance of MyQueueable, it will run and then chain MySecondQueueable, which will run after MyQueueable completes.