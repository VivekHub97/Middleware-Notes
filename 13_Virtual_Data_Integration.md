# Q. What are Federated DBMS?

A **Federated Database Management System (Federated DBMS)** is an approach to virtual data integration that provides a unified, integrated view of multiple, heterogeneous, and distributed data sources while preserving their autonomy. Key characteristics include:

- **Global Federated Schema**: It uses a global schema based on a canonical data model to describe the integrated view of all participating data sources.
- **Distribution Transparency**: Applications can query multiple data sources as if they were a single database.
- **Handling Heterogeneity**: It addresses heterogeneity in data models, query languages, and schemas through schema integration and mapping techniques.
- **Autonomy Preservation**: Participating systems retain control over their local operations and schemas.
- **Schema Creation Alternatives**:
  - *Bottom-up*: Integrates existing schemas from individual systems.
  - *Top-down*: Designs a new global schema and maps local schemas to it.

# Q. What are the architecture components of Federated DBMS?

The architecture of a Federated DBMS, as shown in the diagram, consists of the following components:

1. **Local Schema**:
   - Represents the conceptual schema of the local database.
   - Based on the local data model (e.g., relational, object-oriented).

2. **Component Schema**:
   - Describes the local database using a global canonical data model.
   - Resolves data model heterogeneity between local schemas and the global schema.

3. **Export Schema**:
   - Defines a subset of the local schema to be made available for global applications.
   - May be under the control of the local system and could replace the component schema in some cases.

4. **Federated Schema**:
   - A global schema that integrates and renames elements from export schemas.
   - Resolves structural, schematic, and semantic heterogeneity using mechanisms like views.

5. **External Schema**:
   - Tailored for specific applications or user groups.
   - Provides a customized view of the federated schema, similar to traditional external schemas in databases.

Each layer serves a specific purpose to ensure integration, transparency, and autonomy while addressing heterogeneity across distributed systems.
![screenshot](Federated_DBMS.png)

# Q. What are the Mediator-based Information Systems?

**Mediator-based Information Systems** are a generalization of architectures for virtual data integration, such as Federated DBMS. They are designed to integrate multiple, distributed, and heterogeneous data sources by using two key components:

1. **Wrappers**:
   - Wrappers encapsulate individual data sources and provide uniform access to their data.
   - They address heterogeneity in data models, APIs, and query languages by transforming local data into a common format.

2. **Mediators**:
   - Mediators interact with one or more wrappers to provide advanced services like query transformation, metadata management, and data-level integration.
   - They also manage distribution transparency by offering a global mediator schema.

### Features of Mediator-based Systems:
- **Autonomy Preservation**: Data sources remain autonomous while participating in the integration process.
- **Architecture Variations**:
  - Mediators can be nested for complex integration scenarios.
  - A single wrapper may handle multiple sources of the same type.
  - Applications can directly access wrappers if needed.

![screenshot](Mediator-based-IS.png)

# Q. What is Garlic and its architecture?

**Garlic** is a middleware system developed by IBM to integrate heterogeneous and distributed data sources. It uses a wrapper-based architecture to standardize how diverse data is described and accessed while dynamically determining the role of wrappers in query processing. This approach enables efficient integration and query optimization across various types of data sources.

### Basic Architecture of Garlic:
The architecture consists of the following components:

1. **Clients**:
   - Applications or users that interact with the Garlic system to run queries on integrated data sources.

2. **Garlic Middleware**:
   - **Query Processor**: Responsible for query planning, optimization, and execution. It collaborates with wrappers to determine which parts of a query can be processed locally at the data source and which require compensation by Garlic.
   - **Metadata Repository**: Stores metadata about the data sources, including their schemas, capabilities, and configurations. This information is used for query optimization and integration.

3. **Wrappers**:
   - Encapsulate individual data sources (e.g., relational DBMS, XML files, image archives, messaging systems) and provide a uniform interface for accessing their data.
   - Wrappers supply metadata about their capabilities and can evolve from simple implementations (e.g., basic scans) to fully supporting advanced query functionalities.

4. **Data Sources**:
   - These are the underlying heterogeneous systems being integrated, such as relational databases, XML files, or other specialized repositories.

### Key Features:
- **Dynamic Query Planning**: Wrappers and the middleware dynamically determine how queries are processed.
- **Function Compensation**: The Garlic query engine compensates for any limitations in wrapper functionality.
- **Extensibility**: New data sources can be added by creating new wrappers or reusing existing ones.
- **Standardization**: Data sources are modeled as object collections using a common language (Garlic Data Language).

This architecture allows Garlic to provide seamless integration of diverse data sources while leveraging their specific capabilities for efficient query processing.

![screenshot](Garlic.png)

# Q. Explain method calls and query planning of Garlic.

### Method Calls in Garlic:
Garlic supports two types of method calls to interact with data sources:

1. **Implicitly Defined Methods**:
   - These are standard *get* and *set* accessor methods for attributes of objects in the data source.

2. **Explicitly Defined Methods**:
   - These are custom methods defined for specific operations (e.g., displaying an image file or executing a complex query).

**Invocation Mechanisms**:
- **Stub Dispatch**:
  - Used when the data source provides object class libraries.
  - Example: For an image repository, the wrapper extracts the file name from the object identifier (OID), receives parameters (e.g., device name), and uses a class library to display the image.
- **Generic Dispatch**:
  - Schema-independent mechanism where the wrapper translates method calls into queries.
  - Example: A relational wrapper translates method calls into SQL queries using metadata about attributes, relations, and values.

---

### Query Planning in Garlic:
### Query Planning in Garlic (Slides 17-19)

The **query planning process in Garlic** follows a **bottom-up approach**, where the optimizer collaborates with wrappers to generate efficient query execution plans. Below is a detailed explanation of the process:

---

### **Bottom-Up Query Planning Steps**

1. **Single Collection Access Plan**:
   - The Garlic optimizer starts by identifying query fragments that can be executed on individual collections (i.e., data sources) without referencing other sources.
   - These fragments are sent to the respective wrappers, which return one or more query plans for processing the fragment or parts of it.
   - The minimal requirement for a wrapper is to provide all objects in a collection if no advanced query capabilities are available.

2. **Local Join Plan**:
   - For collections within the same data source, the optimizer plans join operations locally.
   - Wrappers provide plans for multi-way joins when all tables involved reside in the same data source.

3. **Cross-Source Join Plan**:
   - Joins across multiple data sources are planned next. This involves identifying intermediate results from one source that need to be joined with results from another source.

4. **Final Plan Assembly**:
   - The final query plan is completed by adding any additional processing required within the Garlic query engine itself.
   - This includes *function compensation*, where Garlic handles parts of the query that cannot be processed by wrappers due to their limitations.

---

### **Wrapper Participation in Query Planning**
Wrappers play an active role in query planning by providing:
- **Plan Fragments**: Wrappers generate single-collection access plans (`planaccess`) and multi-way join plans (`planjoin`).
- **Cost Estimates**: Wrappers return cost information for each plan, enabling the Garlic optimizer to choose the most efficient option.
- **Special Plans**: Wrappers can also generate special plans like bind plans (`planbind`) for processing inner streams of bind joins.

---

### **Why Bottom-Up Approach?**
The bottom-up approach is chosen for several reasons:
1. **Maximizing Wrapper Capabilities**:
   - By starting with smaller, self-contained query fragments, Garlic ensures that each wrapper processes as much of the query as possible, leveraging its specific capabilities.
   
2. **Minimizing Data Transfer**:
   - Processing as much of the query as possible at the data source reduces the need to transfer large amounts of data across the network.

3. **Efficient Cost Optimization**:
   - Wrappers provide cost estimates for their plans, allowing Garlic to select the most cost-effective execution strategy.

4. **Flexibility with Function Compensation**:
   - Any parts of the query that cannot be handled by wrappers are compensated within Garlic’s query engine, ensuring robust and complete query execution.

---

### Example (Slide 19):
Consider a query to find 5-star hotels in small towns in Greece located on the beach. The steps would involve:
1. Generating single-collection access plans for hotels, cities, and countries.
2. Planning local joins within a relational repository (e.g., joining hotels with cities based on city names).
3. Planning cross-source joins (e.g., joining city-country relationships across different sources).
4. Finalizing the plan with any additional processing required by Garlic’s engine.

This systematic approach ensures efficient and effective integration of heterogeneous data sources while optimizing performance and resource usage.


# Why Bottom-Up Approach?
The bottom-up approach is used because it allows Garlic to maximize the use of each data source's capabilities while minimizing unnecessary data transfer. Here’s how it works:

- **Wrapper Participation**: Wrappers actively participate in query planning by providing alternative plans and cost estimates for processing query fragments.
- **Query Fragmentation**: The optimizer identifies the largest possible query fragment that can be processed by a single data source without referencing other sources.
- **Cost Optimization**: Wrappers return one or more plans for processing these fragments, along with cost information. The optimizer then selects the most efficient plan.
- **Function Compensation**: If a wrapper cannot fully process a fragment, Garlic compensates by handling unsupported parts of the query within its own engine.

This approach ensures efficient query execution by leveraging the strengths of individual data sources while maintaining flexibility to handle their limitations.


## Q. What are bind plan and join plan?

### Join Plan and Bind Plan in Garlic Architecture (Slides 21 and 22)

In Garlic, query planning involves generating **join plans** and **bind plans** to handle complex queries involving multiple collections or sources. These plans are created by wrappers and the Garlic query engine to facilitate efficient query execution.

---

### **Join Plan**
- **Definition**: A join plan is a query plan generated by a wrapper to handle multi-way joins between tables or collections that reside within the same data source.
- **Purpose**:
  - To process join operations locally within a single data source, avoiding unnecessary data transfer to the Garlic query engine.
  - Supports joins that occur in application queries or during the resolution of path expressions.
- **How It Works**:
  - The wrapper identifies all tables involved in the join that reside in its data source.
  - It generates a plan for executing the join locally, providing cost estimates and execution details to the Garlic optimizer.
- **Example**:
  - If a relational repository contains tables for `Hotels` and `Cities`, a join plan could be created to combine these based on a shared attribute like `city_name`.

---

### **Bind Plan**
- **Definition**: A bind plan is a specialized query plan used to process the inner stream of a *bind join*.
- **Purpose**:
  - To efficiently handle cases where one side of the join depends on results from another source (e.g., nested loop joins).
  - The bind plan processes subsets of data iteratively based on input provided by the outer stream.
- **How It Works**:
  - The wrapper creates a plan for handling iterative queries, where each iteration processes a subset of data filtered by parameters from the outer stream.
  - This allows for efficient processing of dependent joins across multiple sources.
- **Example**:
  - Suppose we are joining `Countries` with `Cities`, and each city is fetched iteratively based on its country. The bind plan would handle fetching cities for each country provided by the outer stream.

---

### Why Are These Plans Important?
1. **Efficiency**:
   - Join plans minimize data transfer by processing joins locally within data sources whenever possible.
   - Bind plans optimize dependent joins, reducing redundant computations.

2. **Wrapper Collaboration**:
   - Wrappers actively participate in query planning by providing these plans, ensuring that their specific capabilities are utilized effectively.

3. **Cost Optimization**:
   - Both join and bind plans include cost estimates, allowing Garlic’s optimizer to choose the most efficient execution strategy.

By combining these plans with function compensation (for unsupported operations), Garlic achieves robust and efficient query execution across heterogeneous data sources.




# Q. What are Multi Database?

### **Multi-Database Systems (Slide 31)**

**Multi-database systems** are a type of virtual data integration architecture characterized by the **loose coupling** of autonomous systems. These systems prioritize preserving the independence of participating databases while enabling integrated access to their data.

---

### **Key Characteristics of Multi-Database Systems**:
1. **Autonomy**:
   - Participating databases are autonomous, meaning they retain control over their schema and operations.
   - This autonomy includes *schema design autonomy*, allowing databases to define and manage their schemas independently.

2. **No Global Schema**:
   - Unlike Federated DBMS or Mediator-based systems, multi-database systems do not rely on a global schema.
   - Instead, each database exposes its data through local export schemas, which describe the data available for integration.

3. **Integration at Application Level**:
   - The actual integration of data from multiple sources is performed by the application rather than the system itself.
   - Applications directly reference the export schemas of individual databases to access and combine data.

4. **Transparency Provided**:
   - Supports only **location transparency** (the physical location of data is hidden) and **physical distribution transparency** (data appears as if it is stored in one place).
   - Does not provide higher-level transparency, such as schema or semantic transparency.

5. **Handling Heterogeneity**:
   - Data model heterogeneity (e.g., relational vs. object-oriented) must be addressed either by the local data source or through multi-database query languages.

---

### **Multi-Database Query Languages**:
To facilitate access to multiple databases in a single query, multi-database systems often use specialized query languages. These languages directly reference export schemas and may include mechanisms to bridge schematic heterogeneity.

Examples:
- **SchemaSQL**: Extends SQL to handle schematic heterogeneity by allowing variables in SQL's `FROM` clause to range over databases, tables, attributes, and distinct values.
- **FIRAFISQL**: Provides a federated relational model with dynamic schema handling and new operators for transforming metadata into data and vice versa.

---

### **Limitations of Multi-Database Systems**:
- Lack of a global schema makes integration more complex and shifts the burden to application developers.
- Limited transparency compared to Federated DBMS or Mediator-based systems.
- Applications must explicitly handle heterogeneity and integration logic, which can increase development effort.

---

In summary, multi-database systems focus on loose coupling and autonomy while providing minimal integration support. They are suitable for environments where autonomy is critical but require more effort at the application level for seamless data integration.

# Q. What is SchemaSQL?

**SchemaSQL: Overview and Problems**

SchemaSQL is an extension of SQL designed to address schematic heterogeneity in multi-database systems. It enables querying and transforming metadata alongside data, making it suitable for integrating data across databases with different schema representations. Unlike standard SQL, SchemaSQL allows dynamic schemas where the structure of query results depends on the data present in input relations. This capability is achieved through an extended `FROM` clause that enables iteration over databases, tables, attributes, and even distinct values of attributes[1].

### **Key Features of SchemaSQL**
1. **Dynamic Result Schemas**: The schema of query results can dynamically adapt to the input data, which is particularly useful for handling schematic heterogeneity.
2. **Extended FROM Clause**: Allows range variables to iterate over databases, relations, attributes, and values, enabling flexible queries across heterogeneous schemas.
3. **Metadata Transformation**: Facilitates transforming metadata into data and vice versa, aiding integration tasks.

### **Problems with SchemaSQL**
Despite its innovative approach, SchemaSQL faces several challenges:
1. **Undefined Merge Operations**: When merging data from multiple sources, SchemaSQL does not define how conflicting values should be handled. For example, if two tuples from different sources have conflicting values for the same attribute, SchemaSQL does not specify which value to retain[1].
2. **Aggregation Over Dynamic Columns**: Aggregating data across a variable number of columns poses semantic challenges. The interpretation of such queries can vary depending on the context or view definition[1].
3. **Context-Dependent Semantics**: The meaning of SchemaSQL queries can change depending on their context (e.g., when placed in a view definition), leading to potential inconsistencies or unexpected results[1].
4. **Complexity in Usage**: The flexibility of SchemaSQL introduces complexity in query formulation and interpretation, making it less intuitive for users accustomed to traditional SQL.

### **Relation to Tree Similarity**
SchemaSQL itself is not directly related to checking similarity in tree structures. However, its ability to dynamically iterate over hierarchical metadata (such as database schemas) aligns with techniques used in structural schema matching algorithms like TreeMatch in Cupid[2]. These algorithms use tree representations to compare schema structures and identify correspondences. While SchemaSQL focuses on querying and transforming metadata dynamically, tree similarity methods focus on evaluating structural relationships between schema elements.

In summary, SchemaSQL is a powerful tool for addressing schematic heterogeneity but comes with challenges related to merge operations, aggregation semantics, and query complexity. While it does not directly involve tree similarity checking, its dynamic handling of hierarchical metadata complements structural matching approaches used in schema integration tasks.

# Q. Explain FIRA

**FIRA: Federated Interoperable Relational Algebra**

FIRA (Federated Interoperable Relational Algebra) is an extension of the traditional Relational Algebra (RA) designed to address schematic heterogeneity in federated database systems. It provides mechanisms to query and restructure both metadata and data directly within the relational model. FIRA supports dynamic schemas and metadata integration, making it suitable for federated environments where data sources have varying schemas.

### **Key Features of FIRA**
1. **Federated Data Model**:
   - Extends the relational model to include metadata.
   - Federated tuples are mappings of attribute names to values, allowing dynamic schema changes.
   - Federated relations and databases consist of sets of such tuples, enabling schema evolution.

2. **New Operators**:
   FIRA introduces six operators that enhance its ability to handle schematic heterogeneity:
   - **Drop-Projection**: Removes specified attributes from relations or federated databases.
   - **Down**: Converts metadata into data by creating a relation for metadata and forming its cross-product with the original relation.
   - **Attribute Dereference**: Dynamically determines attribute values based on other attributes' values.
   - **Generalized Union**: Computes the outer union of all relations in a database.
   - **Transpose**: Converts distinct values of a column into new attributes, dynamically restructuring relations.
   - **Partition**: Creates a federated database with one relation for each distinct value in a specified column.

3. **Dynamic Schema Handling**:
   - Operations can modify relation schemas dynamically, making FIRA robust against schema evolution.

4. **Transformational Completeness**:
   FIRA is capable of transforming any form of relational metadata into data and vice versa, ensuring flexibility in integration tasks.

### **Problems Addressed by FIRA**
FIRA resolves several issues inherent to traditional RA and SQL approaches in handling schematic heterogeneity:
1. **Schema Evolution Robustness**:
   - Traditional SQL queries often break when schemas change (e.g., new attributes or relations are added, or existing ones are removed). FIRA's operators dynamically adapt to schema changes, avoiding such issues.
   
2. **Complexity in Query Maintenance**:
   - SQL view definitions hard-code schema details, requiring frequent updates when schemas evolve. FIRA eliminates this by providing operators that inherently support dynamic schemas.

3. **Integration Across Heterogeneous Sources**:
   - FIRA's federated data model and operators allow seamless integration across sources with different schemas, overcoming challenges posed by schematic heterogeneity.

### **Relation to Schema Evolution**
FIRA is said to be more robust under schema evolution than traditional SQL or RA because:
- Its operators dynamically adapt to changing schemas without requiring manual query modifications.
- It allows users to specify transformations generically, reducing maintenance overhead when schemas evolve.

In summary, FIRA provides a theoretically sound framework for resolving schematic heterogeneity in federated systems. Its dynamic schema capabilities and robust operators make it well-suited for environments where schema evolution is frequent and integration across heterogeneous sources is necessary.