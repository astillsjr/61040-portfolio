# Assignment 2: Functional Design  
## Part 3 – Concept Design

**concept** TaskManager [User]  
**purpose** creating and display tasks users intend to complete  
**principle** users define tasks they want to complete; these tasks can then be used to trigger nudges, places bets, and record emotional logs  

**state**
  - a set of Users with
    - a set of Tasks

  - a set of Tasks with
    - a title String
    - a description String
    - a scheduledStartTime Timestamp
    - an actualStartTime Timestamp
    - a status (`NotStarted`, `InProgress`, `Completed`)

**actions**
  - `create (user: User, title: string, description: string, scheduledStartTime: TimeStamp): (task: Task)`
    - **requires**: task title is unique for user; scheduleStartTime has not already passed
    - **effect**: creates a new task and adds it to the user's task list

  - `remove (user: User, task: Task): ()`
    - **requires**: task exists and belongs to user
    - **effect**: remove task from user's set of tasks 

  - `update (user: User, task: Task, updates: Partial<Task>): ()`
    - **requires**: task exists and belongs to user
    - **effect**: updates the task with new values

  - `markStarted (user: User, task: Task, actualStartTime: Timestamp): ()`
    - **requires**: task exists and belongs to user; task is not yet started
    - **effect**: marks the task as started

  - `markCompleted (user, task: Task): ()`
    - **requires**: task exists and belongs to user
    - **effect**: deletes a short URL and its association with a target URL


**concept** NudgeEngine [User, Task]  
**purpose** creates and delivers scheduled or context-aware nudges that encourage users to start tasks  
**principle** when a user creates a task, a nudge is generate to remind the user to start the task early  

**state**
  - a set of Nudges with
    - a user User
    - a task Task
    - a deliveryTime Timestamp
    - a response (`Accepted`, `Ignored`, `Snoozed`, `None`)

**actions**
  - `generate (user: User, task: Task, deliveryTime: Timestamp): (nudge: Nudge)`
    - **requires**: task exists and belongs to user; deliveryTime has not already passed
    - **effect**: creates a new nudge 

  - `delete (user: User, nudge: Nudge): ()`
    - **requires**: nudge exists and belongs to user
    - **effect**: removes the nudge from the set of nudges

  - `system nudgeUser (): (nudge: Nudge)`
    - **requires**: deliveryTime for some Nudge is in the past 
    - **effect**: returns the nudge that needs to be sent

  - `recordResponse (nudge: Nudge): ()`
    - **requires**: deliveryTime for some Nudge is in the past 
    - **effect**: records the effect of the nudge.

**concept** EmotionLogging [User, Task]  
**purpose** capture and visualize how users feel before and after working on tasks  
**principle** when a user creates a tasks, they are prompted to log their initial emotions towards that task; after completion, users are once again prompted to log their final emotions towards the same task  

**state**
  - a set of Users with
    - a set of EmotionEntries

  - a set of EmotionEntries with
    - a task Task
    - a timeStamp TimeStamp
    - an emotionType (`BeforeStart`, `AfterEnd`)
    - a set of emotions (`Dread`, `Neutral`, `Focused`, `Anxious`, `Excited`, etc.)

**actions**
  - `logEmotion (user: User, task: Task, emotionType: Enum, emotions: set<Enum>): (entry: EmotionalEntry)`
    - **requires**: no shortening exists for shortUrlBase/shortUrlSuffix
    - **effect**: forms and saves a new short URL by appending suffix to base and associates it with targetUrl

  - `getEmotionalTrends (user: User): (visualizedData: Graph)`
    - **requires**: a shortening exists for shortUrl
    - **effect**: returns the target URL associated with a short URL

**concept** MicroBetting [User, Task]  
**purpose** adds gamified accountability by letting users place small bets (using virtual points) on starting tasks on time.  
**principle** users wager virtual points on their ability to start tasks by specified times; upon successfully completing a wager, they recieve a reward

**state**
  - a set of Users with
    - a set of Bets

  a set of Bets with
    - a task Task
    - a wager Number
    - a scheduledStart Timestamp
    - a actualStart Timestamp
    - an outcome (`Pending`, `Success`, `Failure`)

**actions**
  - `placeBet (user: User, task: Task, wager: Number, scheduledStart: Timestamp): (bet: Bet)`
    - **requires**: task exists and belongs to user; task has not yet started
    - **effect**: creates a bet for the task

  - `resolveBet (bet: Bet, actualStartTime: Timestamp): (reward?: number)`
    - **requires**: bet exists and associated task has started
    - **effect**: sets the outcome based on whether the task started by the scheduled time and returns the reward (if any)

  - `getUserWagerStats(user: User): (stats: Object)`
    - **effect**: returns user's betting history and success rate

**Essential Syncs**  
**sync** generateNudge  
**when** `TaskManager.create (user: User, scheduledStartTime: Timestamp): (task: Task)`  
**then** `NudgeEngine.generate (user, task, deliveryTime: scheduledStartTime - X min)`  

**sync** promptEmotionBeforeStart  
**when** `TaskManager.markStarted (user: User, task: Task): ()`  
**then** `EmotionLogging.logEmotion (user, task, nonce, emotionType: Enum, emotions: set<Enum>)`  

**sync** promptEmotionAfterCompletion  
**when** `TaskManager.markCompleted (user: User, task: Task): ()`
**then** `EmotionLogging.logEmotion (user, task, emotionType: AfterEnd)`

**sync** resolveMicroBet
**when** `TaskManager.markStarted (user: User, task: Task, actualStartTime: Timestamp): ()` 
**then** `MicroBetting.resolveBet (user, task, actualStartTime)`


**Design Roles & Notes**

- TaskManagement is the anchor. All other concepts operate on tasks and depend on their state (e.g., scheduled, started).

- NudgeEngine acts as a behavioral motivator, initiating engagement with low-pressure prompts.

- EmotionLogging encourages self-awareness, helping users understand and reflect on their avoidance patterns.

- MicroBetting introduces gamified accountability. While the points are virtual, they incentivize consistency and provide a sense of commitment.