# Window functions

```sql
select   row_number() over (order by <field> nulls last) as rownum, *
from     foo_tbl
order by <field>
```