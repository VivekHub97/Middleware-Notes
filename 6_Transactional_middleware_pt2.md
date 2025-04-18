## Q. What are the security features of TP security?

Transaction Processing Security is a critical task in middleware systems that ensures secure execution of transactions across distributed environments. It encompasses several key features:

## Authentication and Identification
- Verifies the identity of principals (human users or application objects)
- Uses mechanisms like user-IDs, passwords, and certificates to provide proof of identity

## Authorization and Access Control
- Determines whether an authenticated principal can invoke specific functions
- Controls access to particular resources or objects
- May be implemented at different levels (request controller, transaction server)

## Communication Security
- **Confidentiality**: Protects against eavesdroppers through encryption
- **Integrity**: Ensures messages aren't modified accidentally or deliberately during transit

## Additional Security Features
- **Auditing**: Logs information to make users accountable for their actions
- Records security-related events to detect and investigate violations
- **Non-repudiation**: Maintains logs so principals cannot deny sending or receiving data/messages

## Implementation in TP Architecture
The security tasks are distributed across the TP system components:

1. **Front-end program**:
   - Establishes secure encrypted communication with end-users
   - Performs user authentication
   - Creates a security context that flows with requests to the request controller

2. **Request controller**:
   - Performs authorization checking based on the received security context/credentials
   - Only invokes transaction servers if checks are successful
   - Passes security context to transaction server with the call

3. **Transaction server**:
   - May perform refined authorization checking
   - Can be configured to accept requests only from trusted request controllers
   - Interacts with database systems using dedicated accounts with sufficient privileges

In container-based security models like EJB, security can be implemented through:
- **Declarative security**: Using annotations/deployment descriptors to specify security roles and access control
- **Programmatic security**: Where application components implement security aspects directly[2]

## Q. What are the differences between declarative secuirty and programmatic security?

Transaction Processing Security includes both declarative and programmatic security approaches, which differ significantly in how security aspects are implemented and managed in middleware systems.

### Declarative Security

Declarative security allows developers to specify security requirements without writing security code directly in the application:

- Security aspects are defined using annotations or deployment descriptors rather than being embedded in application code
- Security roles, access control rules, and authentication requirements are specified separately from business logic
- These security specifications are processed during the deployment phase and mapped to the security environment of the application server
- In EJB containers, this is implemented through elements like `security-role` and `method-permission` in deployment descriptors
- The container automatically performs security checks before method invocation based on these declarations

### Programmatic Security

Programmatic security requires developers to explicitly implement security controls within the application code:

- Application components directly implement security aspects using security APIs
- Security checks are coded directly into business methods
- It provides more fine-grained control for implementing complex security requirements
- Particularly useful for access control that depends on runtime conditions or instance-specific attributes
- For example, a role "clerk" might be allowed to invoke `Loan.approve()` only if `Loan.amount < 10000`
- Uses standardized interfaces to access information about the caller/user and their roles

The key difference is that declarative security separates security concerns from business logic, making security policies easier to modify without changing application code, while programmatic security offers more flexibility for complex, context-dependent security requirements that cannot be expressed through simple role-based declarations[2].

In container-based security models like EJB, both approaches can be combined, with declarative security handling standard access control patterns and programmatic security implementing more specialized requirements[2].

## Q. What is Shared State and sessions?

Transaction Processing Security involves managing shared state and sessions, which are critical components in middleware systems for maintaining information across transactions and interactions.

### Shared State

Shared state refers to information that components of a TP system need to share about users, activities, and the components themselves. This includes:

- **Transaction context**: The transaction ID and related information shared by programs executing within the same transaction
- **User information**: A user's authenticated identity or device address
- **Activity data**: Information about previous messages or temporary data like shopping cart contents
- **Component information**: Identity of transaction managers participating in commit processes

Shared state in TP systems is typically short-lived and transient - it can be discarded after a period (seconds, minutes, or hours) and is primarily shared for convenience or performance reasons[1]. If lost due to failure, it can be reconstructed, unlike long-lived permanent state such as database records that must never be lost.

### Sessions

A session is a lasting connection between two system components (typically processes) that enables them to share state information[1]. Sessions provide several benefits:

- Avoid resending/reprocessing information in every message
- Store shared information such as network addresses of both components
- Maintain access control information
- Store cryptographic keys for secure communication
- Track transaction IDs during transactional remote procedure calls

Sessions can be created explicitly (using a three-way handshake: request-accept-confirm) or implicitly as a side effect of the first RPC request from a client to a server[1]. Each session partner allocates storage/memory to maintain the shared state.

### Stateless vs. Stateful Servers

A key design consideration in TP systems is whether servers should be stateless or stateful:

**Stateless servers**:
- Maintain no application state after servicing a request
- Allow clients to send different requests to different server processes
- Don't need to recover state after failure
- Reduce memory costs of retaining shared state
- Recommended for communication between middle-tier servers and front-end processes

**Stateful servers**:
- Maintain state across multiple client requests
- Needed when user requests require multiple transactions with interdependent results
- Required when servers need to customize interactions based on past user behavior
- Necessary for maintaining secure connections with authentication information
- Used for features like shopping carts that accumulate data over time

## Q. How to manage state?

For stateful applications, there are several approaches to managing state:

### Persistent Storage

1. Store state in a database as part of the transaction. This ensures the state is durable and consistent with other transactional data.

2. Store state in persistent storage, but outside of a transaction. This allows for faster access but may not guarantee consistency with other data.

### Server-Side Storage

Store state in volatile or persistent storage local to the server. This makes the server stateful and requires:

- Labeling the state with the identity of the client and/or server
- Ensuring future requests from the same client go to the server with the stored state

### Client-Side Storage

Return the state to the caller, who must resend it with future requests. This keeps the server stateless but increases network traffic.

### Specific Scenarios

1. For multi-transaction requests: Store state persistently between transactions, labeled with a business process ID.

2. For customizing user interactions: Store user history in a database or server-side cache.

3. For secure connections: Maintain authentication tokens server-side or in encrypted client-side cookies.

4. For shopping carts: Store temporarily server-side or in client-side browser storage.

The choice depends on factors like durability needs, performance requirements, and scalability concerns. Stateful servers provide better performance for some use cases but can limit scalability compared to stateless designs[1][2].

## Q. What are Processes and threads?

In transaction processing systems, processes and threads are fundamental structures that enable concurrent execution of applications.

## Processes

A process is a virtual processor running a program, characterized by a processor state (processor context) consisting of:
- Control thread (content of processor registers, processor stack)
- Address space (memory allocated to the process)

Processes provide:
- Ability to execute programs in parallel
- Protection between different execution environments
- Capability to structure computations into independent execution streams
- Fault containment when programs fail

In traditional time-sharing systems, each display device (terminal or user connection) would have its own dedicated process with exactly one thread executing, and all programs running on behalf of that display device would execute in the same process.

## Threads

A thread is an independent path of execution through a process. In a multithreaded process:
- Multiple control threads exist within a single process/address space
- All threads execute the same program and share the same process memory
- Each thread has its own save area for register values and private data (like the stack)
- Multiple executions of the program run concurrently, one for each thread

The memory structure of a multithreaded process includes:
- Program area (shared by all threads)
- Data area (shared by all threads)
- Individual save areas for each thread (containing registers and stack)

## Advantages of Multithreading

Compared to the time-sharing model where each client connection requires a separate process:
1. Memory efficiency - threads share process memory, reducing overall memory requirements
2. Reduced context switching costs - switching between threads is cheaper than switching between processes
3. Fewer processes to manage - helps operating systems scale better
4. Enables better exploitation of multiprocessor/multicore systems

## Implementation Approaches

Threads can be implemented in two main ways:
1. **By middleware**: The operating system doesn't know about the threads, and the middleware manages thread scheduling. This approach requires the middleware to trap all synchronous I/O operations to prevent the OS from putting the entire process to sleep.

2. **By the operating system**: Most modern operating systems support threads natively, which avoids unnecessary context switching and can exploit thread parallelism in multiprocessor systems.

## Server Classes

As an alternative to multithreaded processes, a server class is a set of single-threaded processes, all running the same program. This approach:
- Emulates a pool of threads
- Provides a good alternative when multithreaded OS processes aren't available
- Allows normal blocking behavior without trapping I/O calls
- Avoids scheduling conflicts and memory corruption problems
- Contains failures to individual processes without bringing down the entire server
- Allows each process to use single-threaded services

However, server classes have disadvantages including higher resource costs (one process per thread) and the need for additional load balancing across servers of the same class.


## Q. What are Server classes and how do they do Application processing?

### Server Classes

A server class is a set of single-threaded processes, all running the same program. This approach serves as an alternative to multithreaded processes for handling transaction processing workloads. Each process in a server class is an independent entity with its own memory space, but all execute the same application code.

Server classes effectively emulate a pool of threads, providing similar functionality to multithreaded servers but with different characteristics. They were particularly popular in classic transaction processing monitors before multithreaded operating systems became ubiquitous.

#### Advantages of Server Classes

- **Normal blocking behavior**: No need to trap synchronous I/O operations since each process can block independently without affecting others
- **Fault containment**: Failure of an individual process doesn't bring down the entire server class
- **No scheduling conflicts**: Avoids potential conflicts between process and thread scheduling
- **Memory protection**: Prevents memory corruption problems that can occur in multithreaded environments
- **Single-threaded service compatibility**: Each process can use services that aren't thread-safe

#### Disadvantages of Server Classes

- **Resource costs**: Higher resource overhead (one process per thread)
- **Load balancing complexity**: Requires additional scheduling mechanisms to balance load across servers of the same class

### Application Processing with Server Classes

Transaction processing monitors manage server classes to handle large workloads efficiently through several key mechanisms:

#### Server Pools

- Groups of pre-started processes waiting for work
- Client requests are dynamically directed to available servers
- Extends to pooling of resource connections (such as database connections)

#### Dynamic Scaling

- TP monitors can automatically extend or shrink the size of server pools based on actual workload
- If there are too few server processes to handle the application workload, the system starts additional instances
- If a server is idle for too long, it can be automatically terminated to conserve system resources

#### Load Balancing

- Work is distributed evenly among members of the pool
- Incoming requests can be prioritized and routed accordingly
- Helps ensure optimal resource utilization across all available servers

This approach allows transaction processing systems to efficiently handle high volumes of requests without requiring one process per client, which would be impractical for large-scale applications serving thousands or millions of users.

![screenshot](Screenshot_20250318_115822.png)