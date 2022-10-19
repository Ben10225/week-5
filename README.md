# week-5

### 要求 3
- 使用 INSERT 指令新增一筆資料到 member 資料表中，這筆資料的 username 和 password 欄位必須是 test。接著繼續新增至少 4 筆隨意的資料。
``` SQL
INSERT INTO member(name, username, password, follower_count) VALUES('Ben', 'test', 'test', 12);
INSERT INTO member(name, username, password, follower_count) VALUES('Tom', 'aa123', 'aa123', 58);
INSERT INTO member(name, username, password, follower_count) VALUES('Amy', 'bb456', 'bb456', 122);
INSERT INTO member(name, username, password, follower_count) VALUES('Gingle', 'cc678', 'cc678', 109999);
```
- 使用 SELECT 指令取得所有在 member 資料表中的會員資料。
``` SQL
SELECT * FROM member;
```
<img width="552" alt="image" src="https://user-images.githubusercontent.com/110615463/196136010-c1b9ca07-1d8f-48ae-a438-e8c28782b5df.png">

- 使用 SELECT 指令取得所有在 member 資料表中的會員資料，並按照 time 欄位，由近到遠排序。
``` SQL
SELECT * FROM member ORDER BY time DESC;
```
<img width="556" alt="image" src="https://user-images.githubusercontent.com/110615463/196136096-733a7257-b839-4ef9-ac0f-7bcc9dec4046.png">

- 使用 SELECT 指令取得 member 資料表中第 2 ~ 4 共三筆資料，並按照 time 欄位，由近到遠排序。( 並非編號 2、3、4 的資料，而是排序後的第 2 ~ 4 筆資料 )
``` SQL
SELECT * FROM member ORDER BY time DESC LIMIT 1,3;
```
<img width="541" alt="image" src="https://user-images.githubusercontent.com/110615463/196328675-3f4c5d77-0fbe-40d9-95fc-063ea368dfed.png">

- 使用 SELECT 指令取得欄位 username 是 test 的會員資料。
``` SQL
SELECT * FROM member WHERE username='test';
```
<img width="536" alt="image" src="https://user-images.githubusercontent.com/110615463/196136343-fe0f81c5-71dd-4ba9-9b5e-24de1f2c17ab.png">

- 使用 SELECT 指令取得欄位 username 是 test、且欄位 password 也是 test 的資料。
``` SQL
SELECT * FROM member WHERE username='test' and password='test';
```
<img width="537" alt="image" src="https://user-images.githubusercontent.com/110615463/196136524-840737d9-f77a-4702-b8b8-a6bfa0788482.png">

- 使用 UPDATE 指令更新欄位 username 是 test 的會員資料，將資料中的 name 欄位改成 test2。
``` SQL
UPDATE member SET name='test2' where username='test';
```
<img width="551" alt="image" src="https://user-images.githubusercontent.com/110615463/196132732-06694762-cb09-4513-afae-419799528721.png">

---
### 要求 4
- 取得 member 資料表中，總共有幾筆資料 ( 幾位會員 )。
``` SQL
SELECT COUNT(*) FROM member;
```
<img width="272" alt="image" src="https://user-images.githubusercontent.com/110615463/196133815-acb7a14b-4855-4871-9fa1-d51c66321d6f.png">

- 取得 member 資料表中，所有會員 follower_count 欄位的總和。
``` SQL
SELECT SUM(follower_count) FROM member;
```
<img width="343" alt="image" src="https://user-images.githubusercontent.com/110615463/196134134-1785b601-337d-4564-8bb7-81bbcddc9e90.png">

- 取得 member 資料表中，所有會員 follower_count 欄位的平均數。
``` SQL
SELECT AVG(follower_count) FROM member;
```
<img width="335" alt="image" src="https://user-images.githubusercontent.com/110615463/196134269-53b8648c-f319-448c-9440-541f1252932f.png">

---
### 要求 5
- 使用 SELECT 搭配 JOIN 語法，取得所有留言，結果須包含留言者會員的姓名。
``` SQL
SELECT member.name, message.content  FROM member INNER JOIN message ON member.id=message.member_id ORDER BY message.time;
```
<img width="913" alt="image" src="https://user-images.githubusercontent.com/110615463/196135029-a6a23bfd-f38d-44ff-940c-1b680831dd1b.png">

- 使用 SELECT 搭配 JOIN 語法，取得 member 資料表中欄位 username 是 test 的所有留言，資料中須包含留言者會員的姓名。
``` SQL
SELECT member.name, message.content FROM member INNER JOIN message ON member.id=message.member_id WHERE member.username='test';
```
<img width="942" alt="image" src="https://user-images.githubusercontent.com/110615463/196135221-ac8fe400-e27c-4c01-b0cf-a8ce86d0bfba.png">

- 使用 SELECT、SQL Aggregate Functions 搭配 JOIN 語法，取得 member 資料表中欄位 username 是 test 的所有留言平均按讚數。
``` SQL
SELECT mb.username, AVG(mg.like_count) FROM message AS mg JOIN member AS mb ON mb.id=mg.member_id WHERE mb.username='test' GROUP BY mb.username;
```
<img width="660" alt="image" src="https://user-images.githubusercontent.com/110615463/196135704-0101aea0-99b7-43b2-86c0-1535ee907c67.png">

---
### 額外要求
- 可以根據留言編號取得該留言有哪些會員按讚。
``` SQL
SELECT m.name FROM pressLike AS p INNER JOIN member AS m ON p.member_id = m.id WHERE p.message_id=6;
```
<img width="769" alt="image" src="https://user-images.githubusercontent.com/110615463/196604419-21c9265a-0ff5-4867-b327-707dd41c5c2c.png">

- 會員若是嘗試對留言按讚：要能先檢查是否曾經按過讚，然後才將按讚的數量 +1 並且記錄按讚的會員是誰。
``` SQL
-- trigger 
DELIMITER $$
CREATE TRIGGER memberPressed BEFORE INSERT ON pressLike 
FOR EACH ROW BEGIN
IF EXISTS(
	SELECT member_id FROM pressLike WHERE message_id=new.message_id AND member_id=new.member_id) THEN
SIGNAL SQLSTATE '45000'
SET MESSAGE_TEXT = 'You Can only like the message once.';
END if;
END $$
DELIMITER ;

INSERT INTO pressLike(member_id, message_id) VALUES(1, 6), (2, 6), (3, 6);
-- 已經按讚後 同一帳戶再按讚就會被拒絕
INSERT INTO pressLike(member_id, message_id) VALUES(1, 6);
-- ERROR 1644 (45000): You Can only like the message once.
```
<img width="487" alt="image" src="https://user-images.githubusercontent.com/110615463/196604927-57abec86-c893-4944-b212-412e053612cd.png">
