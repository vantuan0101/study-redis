### Redis

Redis có tỉ lệ đọc và ghi ( set và get ) nhanh gấp 100 ngàn lần so với MySQL cùng  một cấu hình VPS. 
Redis có thể lưu trữ dữ liệu dưới dạng key-value, có thể lưu trữ dữ liệu dưới dạng list, set, sorted set, hash, bitmap, hyperloglog, geospatial. Redis có thể lưu trữ dữ liệu dưới dạng key-value, có thể lưu trữ dữ liệu dưới dạng list, set, sorted set, hash, bitmap, hyperloglog, geospatial.

### Redis Data Structures
- Keys & Expiration 
- Strings
- Hashes
- Lists
- Sets & Sorted Sets
### Key Commands

Redis keys are a binary safe, made up of a sequence of bytes. Therefore, any byte value can be used.
</br>
Key được tạo thành từ một chuỗi các byte. Do đó, bất kỳ giá trị byte nào cũng có thể được sử dụng.
</br>
Keys là cách duy nhất để truy cập Values
- Key là duy nhất theo định nghĩa.
- Key name là an toàn nhị phân trong Redis.Có nghĩa là bất kỳ chuỗi nhị phân nào cũng có thể được sử dụng làm key
  - "Foo" , 42 , 3.1452 , 0xff
- Chúng có thể có kích thước lên tới 512MB nhưng nên tránh sử dụng quá lớn.
- tên khóa hợp lý so với dung lượng bộ nhớ

***Key space**
- Logical database : 
- Redis không có concert của 1 database mà nó sử dụng thuật ngữ namespacing
- Flat key space : Redis không có cơ chế phân cấp key space . Tất cả các key được lưu trữ trong 1 key space duy nhất.

***Logical database***
- xác định bởi một chỉ mục dựa trên số không. Mặc định là 0

|Key | Scan |
|---|---|
| The KEYS command always blocks entries iterated over all keys. If your database contains millions of keys,
then running this command may block
the database for a long time.| Iterates using a cursor |
| Never use KEYS in production code. If you're looking for a way to find keys in production it's better to use
a different approach like writing a Lua script that uses the Redis SCAN command in iteration. | The SCAN command also blocks, but it only
iterates over a handful of keys at the time.
It then turns a slot reference, which
you can use in subsequent calls to continue your iteration. |
| Useful for debugging and to understand how Redis works internally. | Unlike KEYS, the command is safe of production.|

***Command***`

- `Get key` : GET key
- `MGET key1 key2 key3` : get multiple keys
- `set key value [expiration EX seconds|PX milliseconds] [NX|XX]` : update hoặc tạo nếu không tồn tại key . [expiration EX seconds|PX milliseconds] : set thời gian hết hạn cho key . [NX|XX] : NX : chỉ tạo nếu key không tồn tại . XX : chỉ update nếu key đã tồn tại
- `setex key seconds value` : set key với thời gian hết hạn
- `MSET key1 value1 key2 value2` 
- `del key1`
- `exists key1` : Trả về 0 nếu không tồn tại , 1 nếu tồn tại
- `keys pattern` : Trả về tất cả các key có pattern . Vd : `keys *` : trả về tất cả các key
- `expire key seconds` : Set thời gian tồn tại của key
- `flushdb` : Xóa tất cả các key **trong database**
- `flushall` : Xóa tất cả các key trong **tất cả các database**
- `ttl key` : (Time to live )Trả về thời gian còn lại của key . Trả về -1 Nếu không có set time to live cho key , -2 nếu key hết thời gian tồn tại . 
- `lpush key value1 value2` : Thêm value vào đầu list

### Strings

- Text data
- interger and floating point numbers
- binary data
- Maximum size of a string value is 512MB

Manipulating strings
- `DECRBY key decrement` : Giảm giá trị của key
- `INCRBY key increment` : Tăng giá trị của key
- `INCRRBYFLOAT key increment` : Tăng giá trị của key với số thực

### Hashes Explained
Redis hash là lệnh sử dụng để quản lý các key/value trong đó value có giá trị là hash. Hash là kiểu dữ liệu khá phổ biến, thường được dùng để lưu trữ các object .</br>
Hashes là tập hợp key-value giống một JSON Object, một HashMap Java, hoặc một Dictionary trong Python.
- Có thể thay đổi như add , change , increase , remove
- Hashes lưu value như là string có nghĩa là chúng bằng phẳng. Không nested Arrays hoặc Objects 
- Hashes la schemaless
```redis
HSET user:1 name "name 1"
(integer) 1
HGET user:1 name
"name 1"
```
**Command** 

- `HSET key field value` : Set hoặc tạo giá trị cho field trong hash
- `HGET key field` : Lấy giá trị của field trong hash
- `HGETALL key` : Lấy tất cả các field và giá trị trong hash
- `HINCRRBY key field increment` : Tăng giá trị của field trong hash
- `HDEL key field` : Xóa field trong hash

Sử dụng trong trường hợp
- Rate limiting
- Session caching

### Lists Explained
Redis list là lệnh sử dụng để quản lý các key/value trong đó value có giá trị là một list (danh sách). List là kiểu dữ liệu khá phổ biến, có 2 kiểu list thường dùng là stack (vào sau ra trước) và queue (vào trước ra trước)
```redis
 LPUSH test value1
 (integer) 1
 LPUSH test value2
 (integer) 2
 LPUSH test value3
 (integer) 3
 LRANGE test 0 10
1) "value1"
2) "value2"
3) "value3"
```
**Command**
- `LPUSH key value1 value2` : Thêm value vào đầu list
- `RPUSH key value1 value2` : Thêm value vào cuối list
- `LPOP key` : Lấy value ở đầu list
- `RPOP key` : Lấy value ở cuối list
- `LRANGE key start stop` : Lấy tất cả các value trong list từ start đến stop
- `LLEN key` : Lấy length trong list

### Redis Collections

- Sets : Một tập hợp các phần tử không được sắp xếp , do đó không có vị trí hoặc chỉ mục để lấy các giá trị theo. không có duplicate . 
- Sorted Sets : Một tập hợp các phần tử được sắp xếp

### Sets Explained
Redis set là lệnh sử dụng để quản lý các key/value trong đó value có giá trị là một set (tập hợp). Các giá trị trong tập hợp là duy nhất không bị trùng lặp.
```redis  
redis 127.0.0.1:6379> SADD test value1
(integer) 1
redis 127.0.0.1:6379> SADD test value2
(integer) 1
redis 127.0.0.1:6379> SADD test value3
(integer) 1
redis 127.0.0.1:6379> SADD test value4
(integer) 0
redis 127.0.0.1:6379> SMEMBERS test

1) "value1"
2) "value2"
3) "value3"


```
**Command**
- `SCARD key` : Lấy số lượng member trong set
- `SADD key member1 member2` : Thêm member vào set
- `SREM key member1 member2` : Xóa member khỏi set
- `SISMEMBER key member` : Kiểm tra member có tồn tại trong set hay không
- `SMEMBERS key` : Lấy tất cả các member trong set
- `SUNION key1 key2` : Lấy tất cả các member trong key1 và key2
- `SINTER key1 key2` : Lấy tất cả các member trong key1 và key2
- `SDIFF key1 key2` : Lấy tất cả các member trong key1 mà không có trong key2
          
### Sorted Sets Explained
Redis sorted set là lệnh sử dụng để quản lý các key/value trong đó value có giá trị là một sorted set (tập hợp được sắp xếp theo điểm/độ ưu tiên từ thấp đến cao). Các giá trị trong sorted set là duy nhất không bị trùng lặp.
```redis
redis 127.0.0.1:6379> ZADD test 1 value1
(integer) 1
redis 127.0.0.1:6379> ZADD test 3 value2
(integer) 1
redis 127.0.0.1:6379> ZADD test 2 value3
(integer) 1
redis 127.0.0.1:6379> ZADD test 4 value4
(integer) 0
redis 127.0.0.1:6379> ZADD test 5 value4
(integer) 0
redis 127.0.0.1:6379> ZRANGE test 0 10 WITHSCORES

1) "value1"
2) "1"
3) "value3"
4) "2"
5) "value2"
6) "3"
7) "value4"
8) "5"

```
**Command**
- `ZADD key score1 member1 score2 member2` : Thêm member vào sorted set
- `ZCARD key` : Lấy số lượng member trong sorted set
- `ZCOUNT key min max` : Lấy số lượng member trong sorted set trong khoảng min và max
- `ZRANGE key start stop` : Lấy tất cả các member trong sorted set từ start đến stop
- `ZINCRBY key increment member` : Tăng giá trị của member trong sorted set

### Redis Transaction
Redis transaction cho phép một nhóm các lệnh thực hiện theo thứ tự cho đến khi lệnh cuối cùng được thực hiện xong. Khi này Redis mới cập nhật đồng thời dữ liệu thay đổi bởi nhóm lệnh này. Redis transaction bắt đầu bằng lệnh MULTI và kết thúc bằng lệnh EXEC
```redis
redis 127.0.0.1:6379> MULTI
OK
redis 127.0.0.1:6379> SET test redis
QUEUED
redis 127.0.0.1:6379> GET test
QUEUED
redis 127.0.0.1:6379> INCR visitors
QUEUED
redis 127.0.0.1:6379> EXEC

1) OK
2) "redis"
3) (integer) 1
```
