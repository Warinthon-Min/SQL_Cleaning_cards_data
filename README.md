# Cleaning Data with SQL  

## Type of Data Cleaning Checks  
- Duplicate data  
- Misspelling and typos  
- Inconsistent format  
- Missing values  
- Incorrect data type  
- Inaccurate data  
- Inconsistent casting  
- Whitespace issues  
- Unstandardized unit  
- Inappropriate null values  

### Checking for Missing Values  
มีวิธีการตรวจสอบ missing values หลายรูปแบบ แต่สำหรับตัวอย่างด้านล่างจะเป็นการเลือกคอลัมน์ทั้งหมดใน dataset และนับจำนวน rows แต่ละคอลัมน์ ถ้าผลลัพธ์แสดงจำนวน rows ที่เท่ากันทั้งหมด แสดงว่าไม่มีค่าว่างอยู่ในคอลัมน์ใดคอลัมน์หนึ่ง  

#### ✅ **ตรวจสอบจำนวนข้อมูลที่ขาดหายไปในแต่ละคอลัมน์**  

```sql
-- Count total rows and missing values per column  
SELECT 
    COUNT(*) AS total_rows,
    SUM(CASE WHEN id IS NULL THEN 1 ELSE 0 END) AS missing_id,
    SUM(CASE WHEN client_id IS NULL THEN 1 ELSE 0 END) AS missing_client_id,
    SUM(CASE WHEN card_type IS NULL THEN 1 ELSE 0 END) AS missing_card_type,
    SUM(CASE WHEN card_number IS NULL THEN 1 ELSE 0 END) AS missing_card_number,
    SUM(CASE WHEN expires IS NULL THEN 1 ELSE 0 END) AS missing_expires,
    SUM(CASE WHEN cvv IS NULL THEN 1 ELSE 0 END) AS missing_cvv,
    SUM(CASE WHEN has_chip IS NULL THEN 1 ELSE 0 END) AS missing_has_chip,
    SUM(CASE WHEN num_cards_issued IS NULL THEN 1 ELSE 0 END) AS missing_num_cards_issued,
    SUM(CASE WHEN credit_limit IS NULL THEN 1 ELSE 0 END) AS missing_credit_limit,
    SUM(CASE WHEN acct_open_date IS NULL THEN 1 ELSE 0 END) AS missing_acct_open_date,
    SUM(CASE WHEN year_pin_last_changed IS NULL THEN 1 ELSE 0 END) AS missing_year_pin_last_changed,
    SUM(CASE WHEN card_on_dark_web IS NULL THEN 1 ELSE 0 END) AS missing_card_on_dark_web
FROM new_cards_data;
``` 
### **Check misspelling and type** 
ตรวจสอบ colums ที่เป็น string ถ้ามีการสะกดคำที่ผิด เวลาเราต้องการเรียกดูข้อมูลโดย เฉพาะเจาะจง จะทำให้เราได้ข้อมูลที่ไม่ครบถ้วน และส่งผลต่อการวิเคราะห์ข้อมูลที่ไม่แม่นยำต่อไป อาจเป็นชื่อ ประเทศ ,ชื่อเมือง  ส่วนในตัวอย่างจะเป็นแบรนด์ของบัตรเครดิตการ์ด 
```sql
--find miss spelling from text colums
SELECT DISTINCT lower(card_brand), COUNT(*) AS count_cards_brand 
FROM cards_data                  
GROUP BY card_brand;
```
จากตัวอย่างด้านบนเราเขียน code sql เพื่อเรียกข้อมูลที่ไม่ซ้ำกันของ คอลัมน์ card_bran จากผลลัพธ์ข้อมูลแบ่งมาไม่ได้มีข้อมูลการ์ดที่มีแบรนด์ที่ซ้ำกัน หรือมีการสะกดผิด

### **Find duplicate values**
นข้อมูลบางคอลัมน์ โดยเฉพาะคอลัมน์ primary keys เป็นไปไม่ได้ที่จะมีข้อมูลซ้ำกัน โดยเฉพาะคอลัมน์ อย่างclient_id กับ  card_number, ถ้ามีข้อมูลเกิดซ้ำกันอาจจะเกิดจากดารบันทึกข้อมูลที่ผิดพลาด
```sql
--Find duplicate data 
SELECT client_id, card_number, COUNT(*) 
FROM cards_data 
GROUP BY client_id, card_number
HAVING count(*)>1;
```
### **Check consistent fotmat colums**
ตรวจสอบรูปแบบข้อมูล ว่าเป็นในรูปแบบเดียวกันใหม โดยเฉพาะข้อมูลวันที่ อาจจะมีการบันทึก ปีเป็น พ.ศ หรือ บางข้อมูลอาจใส่มาเป็น คศ ก็ได้ หรือมีการสลับรูปแบใช้  YYYY/mm/dd หรือ dd/mm/YYYY หรือทั้งนี้ข้อมูลทั้งหมดควรอยู่ในรูปแบบเดียวกัน เพื่อสามารถนำข้อมูลไปใช้ได้อย่างมีประสิทธิภาพ เริ่มแรกเราควรเช็คประเภท ของคอลัมน์วันที่ในตารางก่อน
```sql
---Check fotmat date
PRAGMA table_info(cards_data);
```
จากตัวอย่างด้านบนข้อมูลคอลัมน์ expires และ acct_open_date ถูกเก็บอยู่ในรูป text
```sql
----column expire date
SELECT expires
FROM cards_data
WHERE LENGTH(expires) <> 7 
   OR expires NOT LIKE '__/____';
   
-----------------------------------
----column account date open
SELECT acct_open_date
FROM cards_data
WHERE LENGTH(acct_open_date) <> 7
   OR acct_open_date NOT LIKE '__/____';
```
จาก code ด้านบน เราต้องการเช็ตว่าคอลัมน์ expires และ acct_open_date   ต้องมีทั้งหมด 7 ตัวอักษร และอยู่ในรูปแบบ ‘__/____’ โดยให้แสดง ข้อมูลที่ไม่ได้อยู่ในรูปแบบดังกล่าว โดยทำการเช็คทีละคอลัมน์

### **Inappropriate null values**
ในบางคอลัมน์ที่จะแสดงผลลัพธ์แค่ 2 ค่า คือ yes,no ควรตรวจสอบค่าดังกล่าวว่ามีข้อมูลในรูปแบบเพียง yes or no ไหม
```sql
--check Inappropriate null values
SELECT has_chip, card_on_dark_web
FROM new_cards_data
WHERE LOWER(has_chip) NOT IN ('yes','no');

```
### **Delete rows if necessary**
```sql
DELETE FROM new_cards_data WHERE client_id IS NULL;
```
ในการ cleaning data เราควรแน่ใจว่าข้อมูลแบบใดที่สามารถลบออกได้ ซึ่งข้อมูลที่เราลบออกไปจะต้องไม่มีผลทางด้านลบกับการน้ำไปวิเคราะห์ข้อมูล

### **Data cleaning benefits **
- **Accuracy**: ทำให้มั่นใจได้ว่าข้อมูลค่อนข้างมีความแม่นยำ และสมารถเชื่อถือได้ เมื่อต้องใช้ในการตัดสินทางธุรกิจ
- **Efficiency**: การ clean data ที่ดี ยังส่งผลให้ การทำงานของเราบรรลุผลได้เร็วขึ้นด้วย
- **Consistency**: การเตรียมข้อมูลที่ได้ดี จะทำให้เราสามารถนำไปใช้งานได้ง่ายยิ่งขึ้น

**ทั้งนี้การ cleaning data ขึ้นอยู่กับข้อมูลในแต่ละประเภท บางข้อมูลอาจจะมีประเภทข้อมูลที่หลากหลาย อาจจะต้องใช้วิธีการที่หลากหลายมากยิ่งขึ้นในการเตรียมข้อมูล**

[Click here to visit my notions](https://www.notion.so/Cleaning-Data-by-SQL-198641173f238096ab8ccc8c6c3e40fe?pvs=4)





