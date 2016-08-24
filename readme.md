This tool converts sql to elasticsearch dsl

Currently support:

```
[x]sql and expression
[x]sql or expression
[x]equal(=) support
[x]not equal(!=) support
[x]gt(>) support
[x]gte(>=) support
[x]lt(<) support
[x]lte(<=) support
[x]sql in (eg. id in (1,2,3) ) expression
[x]paren bool support (eg. where (a=1 or b=1) and (c=1 or d=1))
[ ]null check expression(is null/is not null)
[ ]join expression
[ ]aggregation like count(*)/group by/having support
```

*Improve : now the query DSL is much more flat*

For example, the following sql :
```
select * from ark_callcenter where create_time between '2015-01-01T00:00:00+0800' and '2017-01-01T00:00:00+0800' and process_id = 0 and status >= 1 and content = 'three man' and phone = '15810324322'
```

will produce dsl like this :

```
{
    "query": {
        "bool": {
            "must": [
                {
                    "range": {
                        "create_time": {
                            "from": "2015-01-01T00:00:00+0800",
                            "to": "2017-01-01T00:00:00+0800"
                        }
                    }
                },
                {
                    "match": {
                        "process_id": {
                            "query": "0",
                            "type": "phrase"
                        }
                    }
                },
                {
                    "range": {
                        "status": {
                            "from": "1"
                        }
                    }
                },
                {
                    "match": {
                        "content": {
                            "query": "三个男人",
                            "type": "phrase"
                        }
                    }
                },
                {
                    "match": {
                        "phone": {
                            "query": "15810324322",
                            "type": "phrase"
                        }
                    }
                }
            ]
        }
    }
}
```
This DSL querystring is generated recursively according to the traversal order of the ast tree generated by sql parser
