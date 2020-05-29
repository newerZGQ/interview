* HandlerThread
  - 集成自Thread，并在run方法中开启loop，由handler不断sendmessage来执行异步任务
  - 退出异步执行的方法，quit：直接退出，不管还有没有没有执行的任务，quitsafety：执行完再退出