---
title: "Developer - Snowflake Documentation"
url: "https://docs.snowflake.com/en/developer"
---

```
from snowflake.snowpark import Session  
from snowflake.snowpark.functions import col  
  
# Create a new session, using the connection properties specified in a file.  
new_session = Session.builder.configs(connection_parameters).create()  
  
# Create a DataFrame that contains the id, name, and serial_number  
# columns in the “sample_product_data” table.  
df = session.table("sample_product_data").select(  
col("id"), col("name"), col("name"), col("serial_number")  
)  
  
# Show the results   
df.show()
```
