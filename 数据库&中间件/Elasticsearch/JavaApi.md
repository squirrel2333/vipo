# Maven环境

```
<dependencies>
    <dependency>
        <groupId>org.elasticsearch</groupId>
        <artifactId>elasticsearch</artifactId>
        <version>7.8.0</version>
    </dependency>
    <!-- elasticsearch 的客户端 -->
    <dependency>
        <groupId>org.elasticsearch.client</groupId>
        <artifactId>elasticsearch-rest-high-level-client</artifactId>
        <version>7.8.0</version>
    </dependency>
    <!-- elasticsearch 依赖 2.x 的 log4j -->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-api</artifactId>
        <version>2.8.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.8.2</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.9</version>
    </dependency>
    <!-- junit 单元测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
</dependencies>
```
# 创建客户端

```
import org.apache.http.HttpHost;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.transport.TransportClient; // Deprecated

import java.io.IOException;

public class ESTestClient {
    public static void main(String[] args) throws IOException {

        // 创建ES客户端
        RestHighLevelClient esClient = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200, "http")));

        // 关闭ES客户端
        esClient.close();
    }
}
```
# 创建索引

```
CreateIndexRequest request = new CreateIndexRequest("users");
CreateIndexResponse createIndexResponse = esClient.indices().create(request1, RequestOptions.DEFAULT);

boolean acknowledge = createIndexResponse.isAcknowledged();
System.out.println("响应状态" + acknowledge);
```
# 查询索引

```
        // 查询索引
        GetIndexRequest request2 = new GetIndexRequest("users");
        GetIndexResponse response2 = esClient.indices().get(request2, RequestOptions.DEFAULT);
        // 查询别名
        System.out.println(response2.getAliases());
        // 查询结构
        System.out.println(response2.getMappings());
        // 查询配置
        System.out.println(response2.getSettings());
```
# 删除索引

```
        // 删除索引
        DeleteIndexRequest request = new DeleteIndexRequest("users");
        AcknowledgedResponse response = esClient.indices().delete(request, RequestOptions.DEFAULT);
        // 响应状态
        System.out.println(response.isAcknowledged());
```
# 文档新增

```
        // 创建ES客户端
        RestHighLevelClient esClient = new RestHighLevelClient(RestClient.builder(new HttpHost("localhost", 9200, "http")));

        // 插入数据
        IndexRequest indexRequest = new IndexRequest();
        indexRequest.index("users").id("003");

        User user = new User();
        user.setAge(80);
        user.setName("wangwu");
        user.setSex("男");

        // 向ES插入数据必须转换成Json格式
        ObjectMapper mapper = new ObjectMapper();
        String userJson = mapper.writeValueAsString(user);
        indexRequest.source(userJson, XContentType.JSON);

        IndexResponse response = esClient.index(indexRequest, RequestOptions.DEFAULT);
        System.out.println(response.getResult());

        esClient.close();
```
# 文档修改

```
        // 修改数据
        UpdateRequest updateRequest = new UpdateRequest();
        updateRequest.index("users").id("003");
        updateRequest.doc(XContentType.JSON, "sex", "女", "age", 18);

        UpdateResponse response = esClient.update(updateRequest, RequestOptions.DEFAULT);
        System.out.println(response.getResult());
```
# 文档查询

```
        // 查询数据
        GetRequest request = new GetRequest();
        request.index("users").id("001");
        GetResponse response = esClient.get(request, RequestOptions.DEFAULT);

        System.out.println(response.getSourceAsString());
```
# 文档的删除

```
        // 文档的删除
        DeleteRequest request = new DeleteRequest();
        request.index("users").id("003");

        DeleteResponse response = esClient.delete(request, RequestOptions.DEFAULT);

        System.out.println(response.getResult());
```
# 文档批量新增

```
        // 批量新增
        BulkRequest request = new BulkRequest();

        request.add(new IndexRequest().index("users").id("003").source(XContentType.JSON, "name", "张三"));
        request.add(new IndexRequest().index("users").id("004").source(XContentType.JSON, "name", "李四"));
        request.add(new IndexRequest().index("users").id("005").source(XContentType.JSON, "name", "王五"));
        BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);
        System.out.println(response.getTook());
        System.out.println(response.getItems());
```
# 文档批量删除

```
        BulkRequest request = new BulkRequest();

        request.add(new DeleteRequest().index("users").id("003"));
        request.add(new DeleteRequest().index("users").id("004"));
        request.add(new DeleteRequest().index("users").id("005"));
        BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);
        System.out.println(response.getTook());
        System.out.println(response.getItems());
```
# 文档全量查询

```
        // 查询索引中全部的数据
        SearchRequest request = new SearchRequest();
        request.indices("users");

        request.source(new SearchSourceBuilder().query(QueryBuilders.matchAllQuery()));
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());
        System.out.println(response.getTook());

        System.out.println("=================");
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```
# 条件查询

```
        // 条件查询
        SearchRequest request = new SearchRequest();
        request.indices("users");

        request.source(new SearchSourceBuilder().query(QueryBuilders.termQuery("name", "张")));
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());
        System.out.println(response.getTook());

        System.out.println("=================");
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```
测试发现，`termQuery("name", "张三")`会搜索不到。只能查到倒排索引中有的，而name被识别为

# 分页查询

```
       // 分页查询
        SearchRequest request = new SearchRequest();
        request.indices("users");

        request.source(new SearchSourceBuilder().query(QueryBuilders.termQuery("name", "张")).from(2).size(2));
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());
        System.out.println(response.getTook());

        System.out.println("=================");
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }

        esClient.close();
```
# 查询排序

```
        // 查询排序
        SearchRequest request = new SearchRequest();
        request.indices("users");

        request.source(new SearchSourceBuilder().query(QueryBuilders.termQuery("name", "张")).sort("age", SortOrder.DESC));
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());
        System.out.println(response.getTook());

        System.out.println("=================");
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```
# 过滤字段

```
        // 过滤字段查询
        SearchRequest request = new SearchRequest();
        request.indices("users");

        SearchSourceBuilder builder = new SearchSourceBuilder().query(QueryBuilders.matchAllQuery());
        String[] excludes = {"age"};
        String[] includes = {};

        builder.fetchSource(includes, excludes);
        request.source(builder);

        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        System.out.println(hits.getTotalHits());
        System.out.println(response.getTook());

        System.out.println("=================");
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```
# 组合查询

```
        // 组合查询
        SearchRequest request = new SearchRequest();
        request.indices("users");

        SearchSourceBuilder builder = new SearchSourceBuilder();
        BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
        boolQueryBuilder.must(QueryBuilders.matchQuery("age", 18));
        boolQueryBuilder.mustNot(QueryBuilders.matchQuery("sex", "男"));
        // boolQueryBuilder.should(QueryBuilders.matchQuery("age", 18));

        builder.query(boolQueryBuilder);

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```
# 范围查询

```
        // 范围查询
        SearchRequest request = new SearchRequest();
        request.indices("users");

        SearchSourceBuilder builder = new SearchSourceBuilder();
        RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("age");
        rangeQueryBuilder.lte(30).gte(18);

        builder.query(rangeQueryBuilder);

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```
# 模糊查询

```
        // 模糊查询
        SearchRequest request = new SearchRequest();
        request.indices("users");

        SearchSourceBuilder builder = new SearchSourceBuilder();
        builder.query(QueryBuilders.fuzzyQuery("name", "张六").fuzziness(Fuzziness.ONE)); // 只偏差一个字符

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```
# 高亮查询

```
        // 高亮查询
        SearchRequest request = new SearchRequest();
        request.indices("users");

        SearchSourceBuilder builder = new SearchSourceBuilder();
        TermsQueryBuilder termsQueryBuilder = QueryBuilders.termsQuery("name", "三");

        HighlightBuilder highlightBuilder = new HighlightBuilder().preTags("<font color = 'red'>").postTags("</font>").field("name");
        builder.highlighter(highlightBuilder);
        builder.query(termsQueryBuilder);

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```
# 聚合查询

```
        // 聚合查询
        SearchRequest request = new SearchRequest();
        request.indices("users");

        SearchSourceBuilder builder = new SearchSourceBuilder();
        AggregationBuilder aggregationBuilder = AggregationBuilders.max("maxAge").field("age");
        builder.aggregation(aggregationBuilder);

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```

```
        SearchRequest request = new SearchRequest();
        request.indices("users");

        SearchSourceBuilder builder = new SearchSourceBuilder();
        AggregationBuilder aggregationBuilder = AggregationBuilders.terms("ageGroup").field("age");
        builder.aggregation(aggregationBuilder);

        request.source(builder);
        SearchResponse response = esClient.search(request, RequestOptions.DEFAULT);

        SearchHits hits = response.getHits();
        for (SearchHit hit : hits) {
            System.out.println(hit.getSourceAsString());
        }
```
