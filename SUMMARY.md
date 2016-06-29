# Summary

* [Introduction 本书简介](README.md)
* [1. Getting Started 入门指南](s01/00_getting_started.md)
   * [1.1 Basic Concepts 基础概念](s01/01_basic_concepts.md)
   * [1.2 Installation 如何安装](s01/02_installation.md)
   * [1.3 Exploring Your Cluster 在你的集群中遨游](s01/03_exploring_your_cluster.md)
       * [1.3.1 Cluster Health 集群是否健康](s01/03_01_cluster_health.md)
       * [1.3.2 List All Indices 列出所有的索引](s01/03_02_list_all_indices.md)
       * [1.3.3 Create an Index 创建索引](s01/03_03_create_an_index.md)
       * [1.3.4 Index and Query a Document 存储和取回一个文档](s01/03_04_index_and_query_a_document.md)
       * [1.3.5 Delete an Index 删除索引](s01/03_05_delete_an_index.md)
   * [1.4 Modifying Your Data 修改数据](s01/04_modifying_your_data.md)
       * [1.4.1 Updating Documents 更新文档](s01/04_01_updating_documents.md)
       * [1.4.2 Deleting Documents 删除文档](s01/04_02_deleting_documents.md)
       * [1.4.3 Batch Processing 批量操作](s01/04_03_batch_processing.md)
   * [1.5 Exploring Your Data 操作数据](s01/05_exploring_your_data.md)
       * [1.5.1 The Search API 查询 API](s01/05_01_the_search_api.md)
       * [1.5.2 Introducing the Query Language 查询语法简介](s01/05_02_introducing_the_query_language.md)
       * [1.5.3 Executing Searches 执行查询操作](s01/05_03_executing_searches.md)
       * [1.5.4 Executing Filters 执行筛选操作](s01/05_04_executing_filters.md)
       * [1.5.5 Executing Aggregations 执行聚合操作](s01/05_05_executing_aggregations.md)
   * [1.6 Conclusion 本章结语](s01/06_conclusion.md)
* [2. Setup 安装指南](s02/00_setup.md)
   * [2.1 Configuration 配置](s02/01_configuration.md)
   * [2.2 Running as a Service on Linux](s02/02_running_as_a_service_on_linux.md)
   * [2.3 Running as a Service on Windows](s02/03_running_as_a_service_on_windows.md)
   * 2.4 Directory Layout
   * 2.5 Repositories
   * 2.6 Upgrading
* [3. Breaking changes](s03/00_breaking_changes.md)
* [4. API Conventions](s04/00_api_conventions.md)
* [5. Document APIs](s05/00_document_apis.md)
* [6. Search APIs](s06/00_search_apis.md)
* [7. Aggregations](s07/00_aggregations.md)
* [8. Indices APIs](s08/00_indices_apis.md)
* [9. cat APIs](s09/00_cat_apis.md)
* [10. Cluster APIs](s10/00_cluster_apis.md)
* [11. Query DSL](s11/00_query_dsl.md)
* [12. Mapping](s12/00_mapping.md)
  * 12.1 Field datatypes
  * 12.2 Meta-Fields
  * 12.3 Mapping parameters Mapping 参数
    * 12.3.1 analyzer
    * 12.3.2 boost
    * 12.3.3 coerce
    * 12.3.4 copy_to
    * 12.3.5 doc_values
    * 12.3.6 dynamic
    * 12.3.7 enabled
    * 12.3.8 fielddata
    * 12.3.9 format
    * 12.3.10 geohash
    * 12.3.11 geohash_precision
    * 12.3.12 geohash_prefix
    * 12.3.13 ignore_above
    * 12.3.14 ignore_malformed
    * 12.3.15 include_in_all
    * 12.3.16 index
    * 12.3.17 index_options
    * 12.3.18 lat_lon
    * 12.3.19 fields
    * 12.3.20 norms
    * 12.3.21 null_value
    * 12.3.22 position_increment_gap
    * 12.3.23 precision_step
    * 12.3.24 properties
    * 12.3.25 search_analyzer
    * 12.3.26 similarity
    * 12.3.27 store
    * 12.3.28 term_vector
  * Dynamic Mapping
  * Transform
* [13. Analysis 解析](s13/00_analysis.md)
   * [13.1 Analyzers 解析器](s13/01_analyzers.md)
       * [13.1.1 Standard Analyzer 标准解析器](s13/01_01_standard_analyzer.md)
       * [13.1.2 Simple Analyzer 简单解析器](s13/01_02_simple_analyzer.md)
       * [13.1.3 Whitespace Analyzer 空格解析器](s13/01_03_whitespace_analyzer.md)
       * [13.1.4 Stop Analyzer 停词解析器](s13/01_04_stop_analyzer.md)
       * [13.1.5 Keyword Analyzer 关键字解析器](s13/01_05_keyword_analyzer.md)
       * [13.1.6 Pattern Analyzer 正则解析器](s13/01_06_pattern_analyzer.md)
       * [13.1.7 Language Analyzers](s13/01_07_language_analyzers.md)
       * 13.1.8 Snowball Analyzer
       * [13.1.9 Custom Analyzer 自定义解析器](s13/01_09_custom_analyzer.md)
   * [13.2 Tokenizers 分词器](s13/02_tokenizers.md)
   * [13.3 Token Filters 标记过滤器](s13/03_token_filters.md)
       * 13.3.1 Standard Token Filter
       * 13.3.2 ASCII Folding Token Filter
       * 13.3.3 Length Token Filter
       * 13.3.4 Lowercase Token Filter
       * 13.3.5 Uppercase Token Filter
       * 13.3.6 NGram Token Filter
       * 13.3.7 Edge NGram Token Filter
       * 13.3.8 Porter Stem Token Filter
       * 13.3.9 Shingle Token Filter
       * 13.3.10 Stop Token Filter
       * 13.3.11 Word Delimiter Token Filter
       * 13.3.12 Stemmer Token Filter
       * 13.3.13 Stemmer Override Token Filter
       * 13.3.14 Keyword Marker Token Filter
       * 13.3.15 Keyword Repeat Token Filter
       * 13.3.16 KStem Token Filter
       * 13.3.17 Snowball Token Filter
       * 13.3.18 Phonetic Token Filter
       * [13.3.19 Synonym Token Filter 同义词标记过滤器](s13/03_19_synonym_token_filter.md)
       * 13.3.20 Compound Word Token Filter
       * 13.3.21 Reverse Token Filter
       * 13.3.22 Elision Token Filter
       * 13.3.23 Truncate Token Filter
       * 13.3.24 Unique Token Filter
       * 13.3.25 Pattern Capture Token Filter
       * 13.3.26 Pattern Replace Token Filter
       * 13.3.27 Trim Token Filter
       * 13.3.28 Limit Token Count Token Filter
       * 13.3.29 Hunspell Token Filter
       * 13.3.30 Common Grams Token Filter
       * 13.3.31 Normalization Token Filter
       * 13.3.32 CJK Width Token Filter
       * 13.3.33 CJK Bigram Token Filter
       * 13.3.34 Delimited Payload Token Filter
       * 13.3.35 Keep Words Token Filter
       * 13.3.36 Keep Types Token Filter
       * 13.3.37 Classic Token Filter
       * 13.3.38 Apostrophe Token Filter
       * 13.3.39 Decimal Digit Token Filter
   * [13.4 Character Filters 字符过滤器](s13/04_character_filters.md)
* [14. Modules](s14/00_modules.md)
* [15. Index Modules](s15/00_index_modules.md)
* [16. Testing](s16/00_testing.md)
* [17. Glossary of terms](s17/00_glossary_of_terms.md)
* [18. Release Notes](s18/00_release_notes.md)

