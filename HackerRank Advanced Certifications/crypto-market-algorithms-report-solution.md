````sql
with cte as(
    select
    c.algorithm as algo,
    quarter(t.dt) as 'quarter',
    sum(volume) as vol
    from coins c
    inner join transactions t
    on c.code = t.coin_code
    where year(t.dt) = 2020
    group by quarter(t.dt), c.algorithm
    order by quarter(t.dt) asc
),

q1 AS(
    select algo, vol
    from cte
    where quarter = 1
),

q2 AS(
    select algo, vol
    from cte
    where quarter = 2
),

q3 AS(
    select algo, vol
    from cte
    where quarter = 3
),

q4 AS(
    select algo, vol
    from cte
    where quarter = 4
),

final AS(
    select
    distinct(q1.algo) as alg,
    (q1.vol) as q1_vol,
    (q2.vol) as q2_vol,
    (q3.vol) as q3_vol,
    (q4.vol) as q4_vol
    from q1
    inner join q2
    on q1.algo = q2.algo
    inner join q3
    on q2.algo = q3.algo
    inner join q4
    on q3.algo = q4.algo
)

select alg, q1_vol, q2_vol, q3_vol, q4_vol
from final;
````

| ALG     | Q1 VOL        | Q2 VOL       | Q3 VOL        | Q4 VOL       |
|---------|---------------|--------------|---------------|--------------|
| Ethash  | 151462.041951 | 72496.626882 | 95519.031415  | 70098.019852 |
| RandomX | 57037.714681  | 31812.179180 | 9759.540424   | 26098.469680 |
| Scrypt  | 159606.943937 | 65609.588182 | 128631.878154 | 78124.251634 |
| SHA-256 | 61246.966303  | 70823.600427 | 77027.632819  | 99171.741237 |
