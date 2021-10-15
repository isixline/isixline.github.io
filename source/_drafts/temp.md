```sql
UPDATE `tool_task` tt
  INNER JOIN(
SELECT `id`, `rank`
  from(
select `id`, @rank:= if(@gen= `tool_id`, @rank+ 1, 0) rank, @gen:= `tool_id`
  from `tool_task`,(
select @rank:= 0, @gen:= null) temp
 order by `tool_id`, `created_at`) b) tti ON tt.`id`= tti.`id`
   SET tt.`display_order`= tti.`rank`
 WHERE tt.`tool_id`= '00968c62-4e75-4640-9501-f4a57903225b' ;
```