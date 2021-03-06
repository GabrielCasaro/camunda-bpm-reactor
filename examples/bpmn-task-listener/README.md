# Task Listener for BPMN

The example demonstrate how to create a task listener for a BPMN process and register it on the event bus.

![Example Process](src/main/resources/Process.png)

# Overview

## How to run it

1. Checkout the project with Git
2. Read and run the unit test

## How it works

### Configuring the Plugin

Add the plugin as dependency in pom.xml:

```xml
...
<dependencies>
  ...
  <dependency>
    <groupId>org.camunda.bpm.extension</groupId>
    <artifactId>camunda-bpm-reactor-core</artifactId>
  </dependency>
  ...
</dependencies>
...
```

And add it as process engine plugin in the process engine configuration:

```java
ProcessEngineConfiguration configuration = new StandaloneInMemProcessEngineConfiguration() {
  {
    // register the plugin
    this.getProcessEnginePlugins().add(CamundaReactor.plugin());
  }
};
```

### Create a Task Listener

Now, create a task lister that should set the due date of a task and add a candidate group.

```java
@CamundaSelector(type = "userTask", event = TaskListener.EVENTNAME_CREATE)
public class TaskCreateListener implements TaskListener {

  @Override
  public void notify(DelegateTask delegateTask) {
    delegateTask.setDueDate(Process.DUE_DATE);

    delegateTask.addCandidateGroup(Process.GROUP_1);
  }
}
```

The annotation `@CamundaSelector` specifies that the listener should be invoked when the process engine enters a user task (type='userTask') and creates a task entity (event='create'). 

At least, the listener must be registered on the event bus. For example, by self-registration in constructor:

```java
public TaskCreateListener() {
  CamundaReactor.eventBus().register(this);
}
``` 
