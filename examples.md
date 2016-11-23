# Logux Protocol Examples

## Examples

Wrong authentication:

```ts
CONNECTED
CLIENT > ["connect", [0, 0], "client1", { credentials: "wrongToken" }]
SERVER < ["error", "wrong-credentials"]
DISCONNECTED
```

Correct synchronization:

```ts
CONNECTED
CLIENT > ["connect", [0, 0], "client1", 0, { credentials: "token" }]
SERVER < ["connected", [0, 0], "server", [1475316481050, 1475316482879]]

CLIENT > ["ping", 0]
SERVER < ["pong", 0]

SERVER < ["sync", 1, { type: 'a' }, [1475316540687, "client2", 0]]
CLIENT > ["synced", 1]

CLIENT > ["ping", 1]
SERVER < ["pong", 1]
DISCONNECTED

CONNECTED
CLIENT > ["connect", [0, 0], "client1", 1, { credentials: "token" }]
SERVER < ["connected", [0, 0], "server", [1475316659892, 1475316660687]]
SERVER < ["sync", 2, { type: 'b' }, [1475316641759, "client2", 0]]
CLIENT > ["synced", 2]
```

Clients may hide some events from each other,
so `added` time could be different:

```ts
CONNECTED
CLIENT > ["connect", [0, 0], "client1", 130, { credentials: "token" }]
SERVER < ["connected", [0, 0], "server", [1475316168379, 1475316169987]]
SERVER < ["sync", 132, { type: 'a' }, [1475316158300, "client2", 0],
                       { type: 'b' }, [1475316158300, "client2", 1]]
CLIENT > ["sync", 1, { type: 'c' }, [1475316168370, "client1", 0]]
CLIENT > ["synced", 132]
SERVER < ["synced", 1]

CLINET > ["ping", 3]
SERVER < ["pong", 150]

CLIENT > ["sync", 4, { type: 'd' }, [1475316404244, "client1", 0]]
SERVER < ["synced", 4]
DISCONNECTED
```
