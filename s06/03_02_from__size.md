# 6.3.2 From / Size 分页查询

通过 `from` 和 `size` 就可以执行分页查询。`from` 指明了分页查询返回的结果的起始位置，而`size`参数则指明了分页查询的页容量。

这两个参数既可以以 request parameters 的形式来设置，也可以在 search body 中设置。默认情况下 `from` 值是 `0`，而 `size` 值是 `10`。

```bash
{
    "from" : 0, "size" : 10,
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```
但要注意的是 `from` + `size` 不能超过索引的 `index.max_result_window` 值，该值默认是 1000，你可以查阅  [Scroll API](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-scroll.html) 来了解另一种更有效的 do deep scrolling (深度滚屏查询)的方式。

***

Pagination of results can be done by using the `from` and `size` parameters. The `from` parameter defines the offset from the first result you want to fetch. The `size` parameter allows you to configure the maximum amount of hits to be returned.

Though `from` and `size` can be set as request parameters, they can also be set within the search body. `from` defaults to `0`, and `size` defaults to `10`.

```bash
{
    "from" : 0, "size" : 10,
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

Note that `from` + `size` can not be more than the `index.max_result_window` index setting which defaults to 10,000. See the [Scroll API](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-scroll.html) for more efficient ways to do deep scrolling.
