# Model View Intent


I have been big fan of model view presenter pattern, I have been using it from 1-2 years. Now I have decided to move to MVI - a new pattern.

#### Why MVI?
To answer this question why MVI, We need to know what is mvi

#### What is MVI?

MVI - Model View Intent means State View Intention, where Model -> State and Intent -> Intention  ( Note: This is not android intent, it is user intention )

#### State
Supose we have screen which is loading right now like this:
![alt text](https://cloud.addictivetips.com/wp-content/uploads/2011/03/LoadingApps.jpg "Logo Title Text 1")

Here the current state can be defined as following:
```
State {
   loading = true
   refreshing = false
   emptyItems = false
   itemsShowing = false
}
```
And when the state changes like the list items are shown the state will be defined as following:
```
State {
   loading = false
   refreshing = false
   emptyItems = false
   itemsShowing = true
}
```

#### MVP to MVI = MVPI
The presenter is kept same, when moving from MVP to MVI. The only difference is in mvp the state of current program is partly known to view and partly known to presenter. This makes program little bit difficult to test and debugg. But in MVI the state is kept in saperate class. We will get in depth as I learn more, but for now this is what I have learn today about MVI.

### References
* https://www.youtube.com/watch?v=KpuFW-jamVo
* http://jakewharton.com/the-state-of-managing-state-with-rxjava/
* http://hannesdorfmann.com/android/mosby3-mvi-1
