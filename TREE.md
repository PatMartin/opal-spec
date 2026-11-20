opal
├── event
│   ├── publish                            POST /event/                    (202 = admitted)
│   ├── data
│   │   ├── event      list | show | remove | clear
│   │   ├── topic      list
│   │   └── execution  list | show
│   ├── rule           list | add | show | update | remove | test
│   ├── action         list | add | show | update | remove | execute
│   ├── profile        list | add | show | update | remove | activate
│   ├── persona        list | add | show | update | remove | validate
│   ├── config         save | load | validate
│   │   └── setting    list | show | set | remove
│   ├── info           version | ping | health | live | ready | stats
│   └── service        start | stop | log
│
├── listen
│   ├── wakeword       list | add | show | update | remove
│   ├── intent         list | add | show | update | remove
│   ├── phrase         list | add | remove                    (scoped: <intent_id>)
│   ├── slot           list | add | show | update | remove
│   ├── slot-value     list | add | remove                    (scoped: <slot_id>)
│   ├── slot-alias     list | add | remove                    (scoped: <slot_id> <value_id>)
│   ├── recognition    start | stop | status | stream | test-text | test-audio
│   ├── data
│   │   └── failed-utterance  list | add | show | update | remove | promote
│   ├── regression     list | add | run | show | update | remove
│   ├── performance    last | summary | history
│   ├── profile        list | add | show | update | remove | activate
│   ├── persona        list | add | show | update | remove | validate
│   ├── config         save | load | validate
│   │   └── setting    list | show | set | remove
│   ├── info           version | ping | health | live | ready | audio-devices
│   └── service        start | stop | log
│
├── look
│   ├── engine         list | show | update | discover | capabilities
│   ├── model          list | show | load | unload
│   ├── predict        image
│   ├── hardware       list | show
│   ├── stream         start | list | show | stop | watch | watch-video | snapshot | metrics
│   ├── profile        list | add | show | update | remove | activate
│   ├── config         save | load | validate
│   │   └── setting    list | show | set | remove
│   ├── info           version | ping | health | live | ready
│   └── service        start | stop | log
│   (no persona surface)
│
├── say
│   ├── voice          list | add | show | update | remove | validate | test
│   ├── engine         list | show | update | validate
│   ├── generate                                            POST /generate
│   ├── stream                                              POST /generate/stream
│   ├── profile        list | add | show | update | remove | activate
│   ├── persona        list | add | show | update | remove | validate
│   ├── config         save | load | validate
│   │   └── setting    list | show | set | remove
│   ├── info           version | ping | health | live | ready
│   └── service        start | stop | log
│
├── think
│   ├── ask                                                 alias: task add + wait, prints answer
│   ├── chat                                                alias: ask bound to a session
│   ├── task           add | list | show | watch | cancel
│   ├── session        add | list | show | remove
│   ├── model          list | show
│   ├── profile        list | add | show | update | remove | activate
│   ├── persona        list | add | show | update | remove | validate
│   ├── config         save | load | validate
│   │   └── setting    list | show | set | remove
│   ├── info           version | ping | health | live | ready
│   └── service        start | stop | log
│
└── look-yolo  (engine adapter contract, openapi/engines/)
    ├── info           show | health
    ├── engines        list | show | discover | capabilities
    ├── models         list | show | load | unload
    ├── hardware       list | show
    ├── predict                                               POST /predict
    │   └── frame                                             POST /predict/frame
    ├── config         save | load | validate
    │   └── setting    list | show | set | remove
    (no profile/persona/service surface)