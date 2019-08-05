# r-elastic-data.table

```r
elk2dt <- function( json_query, index, type ) {
	
	require(elastic)
	require(data.table)
	require(magrittr)
	
	elastic::connect(
		es_transport_schema = "http",
		es_host = "#my_url#",
		es_port = 9200,
		es_user = "#my_user#",
		es_pwd = "#my_password#"
	)
	
	res <- elastic::Search( index = index, type = type, body = json_query )
	n_row <- length(res$hits$hits)
	
	if (n_row == 0) {
		return( data.table() )
	}
	
	lst_rows <- as.list(1:n_row)
	
	for (i in 1:n_row) {
		lst_rows[[i]] <- res$hits$hits[[i]]$`_source` %>% as.data.table()
	}
	
	return( rbindlist( lst_rows, fill = TRUE ) )
	
}
```
