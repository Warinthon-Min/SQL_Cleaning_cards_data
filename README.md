#Cleaning Data by SQL
##Type of data cleaning checck
  -Dupicates data
  -Misspeling and types
  -Inconsistent format
  -Missing values
  -Incorrec data type
  -Inaccurate data
  -Inconsistent casting
  -Whitespace issues
  -Unstandardized unit
  -Inappropriate null values
###Check missing values
มีวิธีการตรวจสอบ missing values หลายรูปแบบ แต่สำหรับตัวอย่างด้านล่างจะเป็นการ เลือกคอลัมน์ทั้งหมด ใน data set และนับจำนวน rows แต่ละคอลัมน์ ถ้าผลลัพธ์แสดงจำนวน rows ที่เท่ากันทั้งหมด แสดงว่า ไม่มีค่าว่างอยู่ใน colums ใดคอลัมน์หนึ่ง
'''
-- count rows in each colums for checking missing values
SELECT 
    COUNT(id),
    COUNT(client_id),
    COUNT(card_type),
    COUNT(card_number),
    COUNT(expires),
    COUNT(cvv),
    COUNT(has_chip),
    COUNT(num_cards_issued),
    COUNT(credit_limit),
    COUNT(acct_open_date) ,
    COUNT(year_pin_last_changed),
    COUNT(card_on_dark_web)
FROM new_cards_data;
'''
