### Q1: Kita akan memberikan promosi untuk customer perempuan di kota Depok melalui email. Tolong berikan data ada berapa customer yang harus kita berikan promosi per masing-masing jenis email.
````sql
select
email,
count(id) as total
from `sales.customer`
where gender = 'Female' and city = 'Depok'
group by email
order by count(id) desc;
````

| email | total |
|-------|-------|
| Gmail | 5941 |
| Yahoo Mail | 729 |
| Hotmail | 728 |


### Q2: Berikan saya 10 id customer dengan total pembelian overall terbesar. Saya akan memberikan diskon untuk campaign 9.9!
````sql
select
customer_id,
sum(total) as total_spend
from `sales.transaction`
group by customer_id
order by sum(total) desc
limit 10;
````

| customer_id | total_spend |
|-------------|-------------|
| 258325 | 10389356 |
| 182640 | 9745524 |
| 176921 | 8801645 |
| 201486 | 7871958 |
| 258916 | 7859005 |
| 333280 | 7690886 |
| 245759 | 6814799 |
| 272726 | 6668899 |
| 178473 | 6472474 |
| 178466 | 6395236 |


### Q3: Bro! Ada berapa produk ya di database kita yang punya harga kurang dari 10000? Mau gue data nih buat flash sale.
````sql
select
count(id) as total_prod
from `sales.product`
where price < 10000;
````

| total_prod |
|------------|
| 50 |


### Q4: Tolong list 5 product_id yang paling banyak dibeli dong, mau kita kasih diskon nih di campaign 11.11.
````sql
select
product_id,
count(product_id) as total_sold
from `sales.transaction`
group by product_id
order by count(product_id) desc
limit 5;
````

| product_id | total_sold |
|-------------|-------------|
| 49 | 29182 |
| 38 | 27064 |
| 39 | 26112 |
| 50 | 24922 |
| 58 | 11643 |


### Q5: Berapa jumlah transaksi, pendapatan dan jumlah produk yang terjual di platform kita sekarang secara bulanan? apakah terjadi kenaikan atau tidak?
````sql
select
date_trunc(date(created_at), month) as 'month',
count(id) as total_transactions,
sum(quantity) as total_sold,
sum(total) as revenue
from `sales.transaction`
group by 1
order by 1
````

| month | total_transaction | total_sold | revenue |
|-------|-------------------|------------|---------|
| 2018-05-01 | 9567 | 483477 | 689565910 |
| 2018-06-01 | 11762 | 592454 | 850287310 |
| 2018-07-01 | 5020 | 253577 | 355238851 |
| 2018-08-01 | 4405 | 222593 | 314474370 |
| 2018-09-01 | 18445 | 935070 | 1335772257 |
| 2018-10-01 | 34608 | 1745805 | 2525342709 |
| 2018-11-01 | 49063 | 2481233 | 3599168275 |
| 2018-12-01 | 48517 | 2450581 | 3541653766 |


### Q6: Saya ingin melakukan pemerataan marketing di perusahaan kita. Boleh saya minta info Total belanja dan rata-rata belanja dari customer kita per kota?
````sql
select
city,
sum(st.total) as total_sold,
avg(st.total) as avg_sold
from `sales.customer` sc
inner join `sales.transaction` st
on sc.id = st.customer_id
group by city
order by sum(st.total) desc;
````

| city | total_sold | avg_sold |
|------|------------|----------|
| Depok | 5337714240 | 72853.905495045008 |
| Tangerang | 2644908252 | 72758.259573063551 |
| Jakarta | 2635133863 | 73043.958947777573 |
| Bogor | 2593747093 | 72668.228868406324 |


### Q7: Ada berapa customer yang memiliki total belanja keseluruhan lebih dari > 200000 ? Tolong di breakdown by tipe storenya ya!
````sql
select
type,
count(distinct(st.customer_id)) as total
from `sales.transaction` st
inner join `sales.store` ss
on st.store_id = ss.id
group by type
having sum(st.total) > 200000
order by sum(st.total) desc
````

| type | total |
|------|-------|
| Online Store | 32025 |
| Offline Store | 4942 |
| Event | 1295 |
| Partnership | 685 |
