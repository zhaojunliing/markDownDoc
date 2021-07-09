### Oracle 合并连续日期时间段


```sql
with gzltrb as ( select t.xmbh, 
   t.ygbh, 
   t.bjje,
   t.ssrq, 
   lead(ssrq, 1, ssrq) over(order by ssrq) next_ssrq,
   lag(ssrq, 1, ssrq) over(order by ssrq) prev_ssrq,
   lead(t.bjje) over(order by ssrq) next_bjje,
   lag(t.bjje) over(order by ssrq) prev_bjje from temp t order by xmbh, ygbh, ssrq )

select x.xmbh, x.ygbh,  x.ssrq as ksrq,
   lead(x.prev_ssrq ,1 ,x.ssrq) over(order by x.ssrq) jsrq,
   x.bjje from ( select g.xmbh, g.ygbh, g.bjje, g.ssrq, 
       (case when g.diff <> 0 then g.ssrq else g.next_ssrq end) as next_ssrq, 
       g.prev_ssrq, 
       g.diff, 
       g.prev_diff from (
    select t.*, 
           (case when t.next_bjje is null then 1 else t.bjje - t.next_bjje end) as diff, 
           (case when t.prev_bjje is null then 1 else t.bjje - t.prev_bjje end) as prev_diff 
           from gzltrb t order by t.xmbh, t.ygbh, t.ssrq 
    )g
    where g.prev_diff <> 0

     ) x
```

