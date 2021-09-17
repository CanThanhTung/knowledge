<h2 align="center">RDS DB PostgreSQL engine upgrades</h2>

<div align="center">
    <img src="assets/postgresql-logo.png"/>
</div>

 Trong quá trình vận hành một RDS DB instance có nhiều nguyên nhân không thể tránh
khỏi việc buộc phải upgrade major/minor của DB engine. Như EOL (End Of Life), bug,
Security, performance, ...!

 Hiện tại AWS RDS có hai loại nâng cấp có thể quản lý cho phiên bản PostgreSQL DB 
của mình:
 
1. OS (Operating System) update
2. DB engine upgrade

Trong bài viết này chúng ta sẽ chỉ tìm hiểu những điều nên làm trong quá trình upgrade major 
của một DB engine
 
* Major version upgrades
  Major Có thể chứa các thay đổi không tương thích với ứng dụng hiện có. Do đó, chúng
  ta phải thực hiện thủ công các nâng cấp các major version. Chúng ta có thể bắt đầu
  nâng cấp major bằng cách sửa đổi DB version. Tuy nhiên, trước khi thực hiện nâng
  cấp major, chúng ta nên nên làm theo từng bước từng bước sẽ được đề cập sau đây.
  Trong quá trình nâng cấp, Amazon RDS cũng nâng cấp tất cả các read replica trong
  cùng một region cùng với primary DB.

* Minor version upgrades
  Ngược lại, nâng cấp minor chỉ bao gồm những thay đổi nhỏ, chúng tương thích với các ứng
  dụng hiện có. Chúng ta có thể bắt đầu nâng cấp minor theo cách thủ công bằng cách
  sửa đổi BD version đang sử dụng. Hoặc chúng ta có thể bật tùy chọn tự động nâng cấp
  minor. DB sẽ được tự động nâng cấp sau khi Amazon RDS kiểm tra và phê duyệt phiên bản mới.

Note: Hiện tại để nâng cấp DB aws sử dụng 
[pg_upgarde](https://www.postgresql.org/docs/current/pgupgrade.html) để nâng cấp postgreSQL
version. Nhưng trong quá trình upgrade pg_upgrade có thể gây ảnh hưởng đến data của bạn,  như 
mất data, performance ...

### Các việc cần làm trước khi nân nâng cấp major version
 Các nâng cấp major verion có thể chứa các thay đổi lớn dẫn đến không tương thích với các phiên bản
trước của DB. Vì vậy aws chỉ cho upgrade thủ công từng major một (10.x.x - > 11.x.x) và cần kiểm tra
kỹ lưỡng từng phiên bản có tương thích với ứng dụng hiện có hay không.

 Vì vậy nên có sự chuẩn bị thật kỹ lưỡng cho DB version muốn nâng cấp. Nên chúng ta nên theo các 
step dưới đây.

1. Chuẩn bị sẵn **parameter-group** cho DB version bạn muốn nâng cấp. Nếu bạn đang sử dụng custom **parameter-group**,
nếu không bạn cũng có thể dùng default **parameter-group** cho new version. 

2. Kiểm tra xem DB engine của bạn có được hỗ trợ bởi DB instace class hay không. 
[DB engine supprot](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.DBInstanceClass.html#Concepts.DBInstanceClass.Support)

3. Kiểm tra tra những thứ đang hoạt động và không được hỗ trợ
  * Đảm bảo trước khi upgrade k có bất kỳ connection, commit hoặc roll back tất cả các transaction
  trước khi thực hiện upgrade
  * Loại bỏ tất cả các reg* data type ngoại trừ **regtype** và **regclass**. pg_upgrade không thể duy trì data
  của data type này.

4. Nếu bạn sử dụng read replica bạn cũng phải sử lý chúng, Một nâng cấp primary DB cũng nâng cấp các read replica
trong cùng một region. không thể nâng cấp các read replica một cách riêng biệt, nhưng việc nâng cấp các read replica
có thể làm tăng rất nhiều thời gian cho việc nâng cấp, downtime lâu cho ững dụng của bạn.
bạn có thể nâng cấp các read replica thành độc lập hoặc xoá đi trước khi nâng cấp sau đó sẽ tạo lại repleca

Quá trình nâng cấp read replica sẽ tạo mới parameter group dựa trên parameter group hiện có. ban chỉ có thể custom
parameter group sau khi quá trình nâng cấp hoàn tất

5. Thực hiện backup data trước khi tiến hành nâng cấp. bạn cũng có thể tạo DB snapshot một cách thủ công hoặc
aws sẽ tự động tạo snapshot trước và sau khi bạn nâng cấp

6. nâng cấp các extensions phù hợp với DB version bạn muốn nâng cấp như
 * address_standardizer
 * address_standardizer_data_us
 * postGIS
 * postgis_tiger_geocoder
 * postgis_topology

7. Loại bỏ các extensions không còn được hỗ trợ khi lên lên version bạn mong muốn hoạc chũng k tương tích trong quá trình nâng cấp
cần loại bỏ và cài đặt lại như **pgRouting**, **tsearch2**, **chkpass**. bạn có thể tìm hiểu thêm tại 
[đây](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.FeatureSupport.Extensions).

8. Loại bỏ **unknown** data type ở PostgreSQL version 10 đã không còn hỗ trợ data type này nữa. Để tìm những colum sử column sử dung data type
này bạn cũng có thể biết bằng cách chạy query bên dưới đây.
```
SELECT DISTINCT data_type FROM information_schema.columns WHERE data_type ILIKE 'unknown';
```

9. Nên thử nâng cấp từ một snapshot của DB PROD trước khi tiến hành nâng cấp cấp DB PROD thật. trong quá trình nâng cấp có rất
nhiều những nguyên nhân có thể dẫn đến việc nâng cấp không thành công khi bạn chỉ thử nghiệm với môi trường STG, DEV. Và bạn cũng nên test 
performance, data có còn nguyên vẹn, thứ tự có bị thay đổi không... 

sau khi nâng cấp thành công nên.
 * chạy **ANALYZE**  để làm mới pg_statistic table.
 * kiểm tra file log khi quá trình nâng cấp của pg_upgrade có hai file pg_upgrade_internal.log và pg_upgrade_server.log