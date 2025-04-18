# Goal of Integration
Provide a homogeneous, integration view on multiple, distributed, autonomous, and heteregeneous systems, components or data sources.  

# Q. What are the 3 integration challenges?

The three fundamental integration challenges, as outlined in the slides, are:

1. **Distribution**:  
   Data is distributed across multiple systems, either physically or logically:  
   - *Physical distribution*: Data resides on geographically separated systems. Challenges include addressing data globally (e.g., using URLs), accessing data with different schemas (e.g., multi-database languages), and optimizing distributed queries.  
   - *Logical distribution*: Data may have several possible storage locations due to partial redundancy or overlapping schema elements. Challenges involve maintaining consistency among redundant data, providing metadata for localization, and resolving duplicates or inconsistencies.

2. **Autonomy**:  
   Systems maintain independence in various aspects and is the MAJOR CAUSE of integration problems:  
   - *Design autonomy*: Freedom in choosing data models, formats, and units.  
   - *Interface autonomy*: Freedom in deciding technical access methods (e.g., HTTP, JDBC) using protocols or supported query languages.  
   - *Access autonomy*: Freedom to control who accesses data and how (e.g., authentication and authorization).  
   - *Judicial autonomy*: Freedom to prohibit integration due to intellectual property or legal constraints.

3. **Heterogeneity**:  
   Differences exist among systems in terms of technical options, data models, structures, syntax, and semantics:  
   - *Technical heterogeneity*: Differences in protocols, formats, APIs, etc.  
   - *Data model heterogeneity*: Use of different data models (e.g., relational vs. hierarchical).  
   - *Syntactic heterogeneity*: Variations in the representation of identical facts (e.g., date formats).  
   - *Structural heterogeneity*: Different modeling of identical concepts within the same data model.  
   - *Semantic heterogeneity*: Differences in the meaning or interpretation of data (e.g., synonyms and homonyms).

These challenges are orthogonal but interrelated and must be addressed for successful integration.

## Q. What are 2 architectural approaches for data integration?

The **two architectural approaches to achieve data integration**, as outlined in the slides, are:


1. **Virtual Integration**:  
   - Data remains in its original sources and is integrated on-demand at query time.  
   - Queries are distributed across multiple data sources, and results are combined dynamically by an integration server.  
   - Examples: Federated DBMS, multi-database systems.

   **Advantages**:  
   - Data is always fresh since queries access live data directly from sources.  
   - Requires minimal storage resources as only metadata is stored centrally.  
   - Preserves autonomy of participating systems.

   **Disadvantages**:  
   - Query response time can be high due to distributed processing.  
   - Complexity is higher due to distributed query planning and execution.  
   - Limited query support depending on source capabilities.

These approaches address different integration scenarios based on requirements for autonomy, performance, and storage resources.

2. **Materialized Integration**:  
   - Data is extracted, transformed, and loaded (ETL) into a single materialized database or data warehouse.  
   - The integration is performed in advance, and the integrated data is stored centrally for query processing and analysis.  
   - Examples: Data replication, data warehousing.

   **Advantages**:  
   - Supports unlimited query capabilities with full SQL functionality.  
   - Enables data cleaning and transformation during the ETL process.  
   - Provides support for historical data storage and analysis.  

   **Disadvantages**:  
   - Data freshness is low due to periodic updates.  
   - High storage requirements for replicated data.  
   - Loss of autonomy as original sources must provide their data centrally.

---


![screenshot](di.png)

# Pros vs cons

![](12-procsons.png)

### Comparison of Materialized vs Virtual Data Integration

- **Data Freshness**: Materialized data integration suffers from low data freshness due to limited update frequency, whereas virtual data integration ensures high freshness as it always accesses live data.

- **Query Response Time**: Materialized integration provides low query response time through local processing, while virtual integration has high response time due to distributed processing.

- **Loss of Autonomy**: Materialized integration requires providing load data, which can impact autonomy. Virtual integration permits query processing without such requirements, preserving source autonomy.

- **Query Support**: Materialized integration supports unlimited and full SQL queries. Virtual integration may have limited query capabilities depending on the source systems.


- **Storage Resources**: Materialized approaches demand high storage resources as they store full datasets. Virtual approaches require minimal storage, utilizing only metadata.


- **Data Cleaning**: Data cleaning is possible in materialized integration due to preprocessing capabilities. In virtual integration, it is often too expensive or infeasible.

- **Historic Data**: Materialized integration supports historic data storage and analysis, whereas virtual integration does not maintain historic data.

- **Complexity**: Materialized integration is less complex, resembling traditional DBMS systems. Virtual integration, on the other hand, involves high complexity due to distributed query planning.



- **Read/Write at Integration**: Materialized integration supports both read and write operations (typically in a data warehouse), whereas virtual integration is typically read-only.


- **Load on Sources**: Materialized integration involves planned load and update processes on source systems. Virtual integration imposes no planned load but relies on real-time querying.

