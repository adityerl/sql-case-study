````sql
with total_rnk as(
    select
    event_id,
    participant_name as pname,
    dense_rank() over(partition by event_id order by score desc) as rnk
    from scoretable
),

rnk_1 as(
    select
    event_id,
    pname as first
    from total_rnk
    where rnk = 1
),

rnk_2 as(
    select
    event_id,
    pname as second
    from total_rnk
    where rnk = 2
),

rnk_3 as(
    select
    event_id,
    pname as third
    from total_rnk
    where rnk = 3
),

temp as(
    select
    r1.event_id event_id,
    first,
    second,
    third
    from rnk_1 r1
    inner join rnk_2 r2
    on r1.event_id = r2.event_id
    inner join rnk_3 r3
    on r2.event_id = r3.event_id
)

select
distinct(event_id) as event_id,
group_concat(distinct first) as first,
group_concat(distinct second) as second,
group_concat(distinct third) as third
from temp
group by 1
order by 1, 2, 3, 4;
````
