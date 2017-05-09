# RxBus

Android reactive event bus that simplifies communication between Presenters, Activities, Fragments, Threads, Services, etc. 

# Download

[ ![Download](https://api.bintray.com/packages/florent37/maven/rxbus/images/download.svg) ](https://bintray.com/florent37/maven/rxbus/_latestVersion)
```java
dependencies {
    compile 'com.github.florent37:rxbus:1.0.0'
}
```

# RxBus in 2 steps

1. Prepare subscribers 

```java
RxBus.getDefault()
                .onEvent("eventName")
                .doOnSubscribe(disposables::add)
                .subscribe(s -> {
                     //called when an event "eventName" is posted      
                });
```

2. Post event

```java
RxBus.getDefault().post("eventName");
```

# Messages types

```java
rxbus.post(String) <-> rxbus.onEvent(String)

rxbus.post(userInstance) <-> rxbus.onEvent(User.class)
```

# Extended Bus

1. Extend RxBus for a specific usage 

```
public class RxBusUser extends RxBus {

    private static RxBusUser instance = new RxBusUser();

    public static RxBusUser getInstance() {
        return instance;
    }

    public Observable<User> onUserChanged(){
        return super.onEvent(User.class);
    }

    public void userChanged(User user){
        super.post(user);
    }

    public void displayUser(boolean display){
        super.post(new DisplayUserCustomEvent(display));
    }

    public Observable<Boolean> onDisplayUser(){
        return super.onEvent(DisplayUserCustomEvent.class)
                .map(DisplayUserCustomEvent::displayUser);
    }

    protected class DisplayUserCustomEvent {

        public boolean displayUser;

        public DisplayUserCustomEvent(boolean displayUser) {
            this.displayUser = displayUser;
        }

        public boolean displayUser() {
            return displayUser;
        }
    }

}
```

2. Register to user change

```java
final RxBusUser rxBusUser = RxBusUser.getInstance();

rxBusUser.onUserChanged()
        .doOnSubscribe(disposables::add)
        .subscribe(user -> display(user));
```

3. Post the user
 
```java
RxBusUser.getInstance().userChanged(user);
```

# Unsubscribe

1. don't forget to add the disposable to a CompositeDisposable `.doOnSubscribe(compositeDisposable::add)`

2. OnDestroy should call `compositeDisposable.clear()` 

License
--------

    Copyright 2017 Florent37, Inc.

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
