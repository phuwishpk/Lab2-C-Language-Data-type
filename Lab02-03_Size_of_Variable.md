# ใบงานที่ 02.03 การเรียนรู้ชนิดข้อมูลในภาษา C บน ESP32 (Wokwi Simulation)

__วัตถุประสงค์__

1. ทำความเข้าใจและสามารถระบุชนิดข้อมูลพื้นฐานในภาษา C (เช่น int, float, char, bool, long, long long, unsigned int, byte, double)

2. เข้าใจแนวคิดเรื่องขอบเขตของข้อมูล (Data Range) สำหรับ ESP32 และผลกระทบจากการเกินขอบเขต (overflow/underflow)

3. เรียนรู้การใช้โอเปอเรเตอร์ sizeof() เพื่อตรวจสอบขนาดของชนิดข้อมูล

4. สามารถประกาศตัวแปรและกำหนดค่าเริ่มต้นให้กับตัวแปรได้อย่างถูกต้อง

### เกี่ยวกับ sizeof() โอเปอเรเตอร์

`sizeof()` เป็นโอเปอเรเตอร์ในภาษา C/C++ ที่ใช้สำหรับหาขนาดของชนิดข้อมูลหรือตัวแปรในหน่วยไบต์ (bytes) มันมีประโยชน์มากในการทำความเข้าใจว่าแต่ละชนิดข้อมูลใช้หน่วยความจำไปเท่าใด ซึ่งอาจแตกต่างกันไปในแต่ละแพลตฟอร์มหรือสถาปัตยกรรมของไมโครคอนโทรลเลอร์ เช่น ขนาดของ int บน Arduino Uno (8-bit) อาจไม่เท่ากับบน ESP32 (32-bit)

### รูปแบบการใช้งาน

sizeof(ชนิดข้อมูล) เช่น sizeof(int)

sizeof(ชื่อตัวแปร) เช่น 

``` c
int myVar; 
sizeof(myVar);
```

ผลลัพธ์ที่ได้จาก `sizeof()` จะเป็นจำนวนเต็มที่บอกถึงจำนวนไบต์ที่ข้อมูลนั้นใช้ในการจัดเก็บ

### 1. ทดลองกับ int (จำนวนเต็ม) และ sizeof():


__วัตถุประสงค์__ 

ทำความเข้าใจการเก็บค่าจำนวนเต็ม ขอบเขต และขนาดของ int บน ESP32

- โค้ดที่ต้องเพิ่มใน `setup()` โดยลบโค้ดเดิมออก ยกเว้นบรรทัด `Serial.begin(115200);`


``` cpp
Serial.println("--- ทดสอบชนิดข้อมูล int (ESP32) ---");
Serial.print("ขนาดของ int: ");
Serial.print(sizeof(int));
Serial.println(" bytes");

int myInteger = 100;
Serial.print("ค่าเริ่มต้นของ myInteger: ");
Serial.println(myInteger);

// ลองกำหนดค่าให้เกินขอบเขตสูงสุดของ int (ประมาณ 2,147,483,647 สำหรับ 32-bit int)
myInteger = 2147483647; // ค่าสูงสุด
Serial.print("myInteger = 2147483647 ผลลัพธ์: ");
Serial.println(myInteger);

myInteger = 2147483647 + 1; // ลองเกินค่าสูงสุด
Serial.print("myInteger = 2147483647 + 1 ผลลัพธ์: ");
Serial.println(myInteger); // สังเกตผลลัพธ์ที่อาจเกิด Overflow

// ลองกำหนดค่าให้ต่ำกว่าขอบเขตต่ำสุดของ int (ประมาณ -2,147,483,648 สำหรับ 32-bit int)
myInteger = -2147483648; // ค่าต่ำสุด
Serial.print("myInteger = -2147483648 ผลลัพธ์: ");
Serial.println(myInteger);

myInteger = -2147483648 - 1; // ลองต่ำกว่าค่าต่ำสุด
Serial.print("myInteger = -2147483648 - 1 ผลลัพธ์: ");
Serial.println(myInteger); // สังเกตผลลัพธ์ที่อาจเกิด Underflow
```

- ดำเนินการจำลองตามวิธีที่ได้ทดลองมาแล้ว

__คำถาม__ 

1.1  int บน ESP32 ใช้กี่ไบต์?
       
     ใช้ 4 ไบต์ (4 bytes)

1.2 จากผลลัพธ์ นักศึกษาสังเกตเห็นอะไรเมื่อค่าเกินขอบเขตของ int บน ESP32? 

      เมื่อกำหนดค่ามากกว่า 2,147,483,647 (เช่น 2147483647 + 1), หรือ
      กำหนดค่าน้อยกว่า -2,147,483,648 (เช่น -2147483648 - 1)
      จะเกิดอาการ "ล้นค่า" (Overflow/Underflow):
      Overflow: ค่าเพิ่มขึ้นจากค่าสูงสุด จะ "วนกลับ" ไปที่ค่าต่ำสุดทันที (เช่น จาก 2,147,483,647 + 1 จะได้ -2,147,483,648)
      Underflow: ค่าลดลงจากค่าต่ำสุด จะ "วนกลับ" ไปที่ค่าสูงสุดทันที (เช่น จาก -2,147,483,648 - 1 จะได้ 2,147,483,647)

### 2. ทดลองกับ float และ double (ทศนิยม) และ sizeof()

__วัตถุประสงค์__ 

ทำความเข้าใจการเก็บค่าทศนิยม ความแม่นยำ และขนาดของ float และ double บน ESP32

__หมายเหตุ__

 บน ESP32, float คือ 4 ไบต์ (Single Precision) และ double คือ 8 ไบต์ (Double Precision) ซึ่งมีความแม่นยำสูงกว่า


- โค้ดที่ต้องเพิ่มใน `setup()` โดยลบโค้ดเดิมออก ยกเว้นบรรทัด `Serial.begin(115200);`


``` c
Serial.println("\n--- ทดสอบชนิดข้อมูล float และ double (ESP32) ---");
Serial.print("ขนาดของ float: ");
Serial.print(sizeof(float));
Serial.println(" bytes");
Serial.print("ขนาดของ double: ");
Serial.print(sizeof(double));
Serial.println(" bytes");

float myFloat = 3.14159265; // ค่า Pi
Serial.print("ค่า myFloat (float): ");
Serial.println(myFloat, 8); // แสดงผลทศนิยม 8 ตำแหน่ง

double myDouble = 3.141592653589793; // ค่า Pi ที่แม่นยำกว่า
Serial.print("ค่า myDouble (double): ");
Serial.println(myDouble, 15); // แสดงผลทศนิยม 15 ตำแหน่ง
```

- ดำเนินการจำลองตามวิธีที่ได้ทดลองมาแล้ว


__คำถาม__


- float และ double บน ESP32 ใช้กี่ไบต์ตามลำดับ?

      float ใช้ 4 ไบต์ (4 bytes) double ใช้ 8 ไบต์ (8 bytes)
      (ยืนยันได้จากผลลัพธ์ของ sizeof(float) และ sizeof(double) ที่แสดงใน Serial Monitor)

- นักศึกษาสังเกตเห็นความแตกต่างของความแม่นยำระหว่าง float และ double อย่างไร? สถานการณ์ใดที่คุณควรเลือกใช้ double แทน float?

      float สามารถเก็บทศนิยมได้ประมาณ 6-7 ตำแหน่ง
      double สามารถเก็บทศนิยมได้ถึงประมาณ 15 ตำแหน่ง
      เมื่อพิมพ์ค่า Pi ลงในตัวแปรทั้งสอง จะเห็นว่า
      ค่าใน float จะเริ่มผิดเพี้ยนหรือมีเลขหลังจุดไม่ตรงจากค่าเดิม หลังจากตำแหน่งที่ 7
      ค่าใน double จะยังคงรักษาค่าทศนิยมได้แม่นยำกว่า float มาก

### 3. ทดลองกับ char (อักขระ) และ sizeof()

__วัตถุประสงค์__

- ทำความเข้าใจการเก็บอักขระ ขนาด และการแปลงเป็นค่า ASCII

- โค้ดที่ต้องเพิ่มใน `setup()` โดยลบโค้ดเดิมออก ยกเว้นบรรทัด `Serial.begin(115200);`

``` c
Serial.println("\n--- ทดสอบชนิดข้อมูล char ---");
Serial.print("ขนาดของ char: ");
Serial.print(sizeof(char));
Serial.println(" bytes");

char myChar = 'Z';
Serial.print("myChar = 'Z' ผลลัพธ์: ");
Serial.println(myChar);
Serial.print("myChar ในรูป ASCII: ");
Serial.println((int)myChar); // แปลง char เป็น int เพื่อดูค่า ASCII

myChar = 122; // 122 คือค่า ASCII ของ 'z'
Serial.print("myChar = 122 ผลลัพธ์: ");
Serial.println(myChar);
```

- ดำเนินการจำลองตามวิธีที่ได้ทดลองมาแล้ว
__คำถาม__

- char ใช้กี่ไบต์? ค่าตัวเลข (ASCII value) มีความสัมพันธ์กับอักขระอย่างไร?

       char ใช้ 1 ไบต์ (1 byte) เห็นได้จาก sizeof(char) ซึ่งแสดงค่า 1
       ในระบบคอมพิวเตอร์ (รวมทั้ง ESP32) ตัวแปร char ถูกออกแบบมาให้ใช้พื้นที่ 8 บิต หรือ 1 ไบต์
  
        ASCII value คือ รหัสตัวเลขที่ใช้แทนอักขระแต่ละตัวในคอมพิวเตอร์
       'A' มีค่า ASCII เป็น 65
       
       'Z' มีค่า ASCII เป็น 90
       
       'a' มีค่า ASCII เป็น 97
       
       'z' มีค่า ASCII เป็น 122
       
       เมื่อกำหนดค่าให้ตัวแปร char ด้วยอักขระ เช่น myChar = 'Z'; ภายในตัวแปรจะถูกเก็บเป็นรหัส ASCII (90)
       เมื่อกำหนดด้วยตัวเลข เช่น myChar = 122; แล้วแสดงผล จะเห็นเป็นตัวอักษรที่ตรงกับรหัส ASCII นั้น (คือ 'z')
       ความสัมพันธ์ คือ หนึ่งต่อหนึ่ง: ตัวเลข 1 ตัว แทน 1 อักขระ
       การแปลง char เป็น int จะได้ค่า ASCII
       การกำหนดค่า ASCII ให้ char จะได้อักขระนั้น

### 4. ทดลองกับ bool (ตรรกะ) และ sizeof():

__วัตถุประสงค์__

- ทำความเข้าใจการเก็บค่าความจริง (true หรือ false) และขนาด

- โค้ดที่ต้องเพิ่มใน `setup()` โดยลบโค้ดเดิมออก ยกเว้นบรรทัด `Serial.begin(115200);`

``` C++
Serial.println("\n--- ทดสอบชนิดข้อมูล bool ---");
Serial.print("ขนาดของ bool: ");
Serial.print(sizeof(bool));
Serial.println(" bytes");

bool myBool = true;
Serial.print("myBool = true ผลลัพธ์: ");
Serial.println(myBool); // true จะถูกแสดงเป็น 1

myBool = false;
Serial.print("myBool = false ผลลัพธ์: ");
Serial.println(myBool); // false จะถูกแสดงเป็น 0
```

- ดำเนินการจำลองตามวิธีที่ได้ทดลองมาแล้ว


__คำถาม__

- bool ใช้กี่ไบต์? true และ false ถูกแสดงผลเป็นค่าใดบน Serial Monitor?

      bool ใช้ 1 ไบต์ (1 byte)

### 5. ทดลองกับ long, long long, unsigned int, unsigned long, unsigned long long (จำนวนเต็มขนาดใหญ่/ไม่มีเครื่องหมาย) และ sizeof():

__วัตถุประสงค์__ ทำความเข้าใจการใช้ชนิดข้อมูลสำหรับจำนวนเต็มที่มีขนาดใหญ่ขึ้นและแบบไม่มีเครื่องหมายบน ESP32 รวมถึงขนาด

- โค้ดที่ต้องเพิ่มใน `setup()` โดยลบโค้ดเดิมออก ยกเว้นบรรทัด `Serial.begin(115200);`

``` C++
Serial.println("\n--- ทดสอบชนิดข้อมูล long, long long, unsigned (ESP32) ---");
Serial.print("ขนาดของ long: ");
Serial.print(sizeof(long));
Serial.println(" bytes");
Serial.print("ขนาดของ long long: ");
Serial.print(sizeof(long long));
Serial.println(" bytes");
Serial.print("ขนาดของ unsigned int: ");
Serial.print(sizeof(unsigned int));
Serial.println(" bytes");
Serial.print("ขนาดของ unsigned long: ");
Serial.print(sizeof(unsigned long));
Serial.println(" bytes");
Serial.print("ขนาดของ unsigned long long: ");
Serial.print(sizeof(unsigned long long));
Serial.println(" bytes");

long myLong = 4000000000L; // long บน ESP32 คือ 32-bit (เท่า int)
Serial.print("myLong (4,000,000,000): ");
Serial.println(myLong); // จะเกิด overflow เพราะเกิน 2.1 พันล้าน

long long myLongLong = 9000000000000000000LL; // long long คือ 64-bit
Serial.print("myLongLong (9,000,000,000,000,000,000): ");
Serial.println(myLongLong);

unsigned int myUnsignedInt = 4000000000U; // unsigned int คือ 32-bit
Serial.print("myUnsignedInt (4,000,000,000): ");
Serial.println(myUnsignedInt);

unsigned long myUnsignedLong = 4000000000UL; // unsigned long คือ 32-bit
Serial.print("myUnsignedLong (4,000,000,000): ");
Serial.println(myUnsignedLong);

unsigned long long myUnsignedLongLong = 18000000000000000000ULL; // unsigned long long คือ 64-bit
Serial.print("myUnsignedLongLong (1.8e19): ");
Serial.println(myUnsignedLongLong);
```

- ดำเนินการจำลองตามวิธีที่ได้ทดลองมาแล้ว

__คำถาม__

- ชนิดข้อมูลจำนวนเต็มแต่ละชนิด (long, long long, unsigned int, unsigned long, unsigned long long) ใช้กี่ไบต์บน ESP32?

      long ใช้ 4 bytes long long ใช้ 8 byte unsigned int ใช้ 4 byte unsigned long 4 byte unsined long long 8 bytes

- บน ESP32, long มีขอบเขตเท่ากับ int หรือไม่? ชนิดข้อมูลใดที่คุณจะใช้หากต้องการเก็บค่าจำนวนเต็มบวกที่ใหญ่ที่สุด?

        ใช่  ทั้ง int และ long มีขนาด 4 ไบต์ (32 บิต) เท่ากันบน ESP32
       ขอบเขตของค่าจะอยู่ที่
       -2,147,483,648 ถึง 2,147,483,647 (signed)
       สำหรับ unsigned int/unsigned long คือ 0 ถึง 4,294,967,295

       ชนิดข้อมูลใดที่คุณจะใช้หากต้องการเก็บค่าจำนวนเต็มบวกที่ใหญ่ที่สุดคือ
        ควรเลือกใช้ unsigned long long
      เพราะเป็นชนิดข้อมูล 64 บิต และไม่มีเครื่องหมาย (unsigned)   
      สามารถเก็บค่าจำนวนเต็มบวกได้ตั้งแต่

0 ถึง 18,446,744,073,709,551,615 (หรือ 2^64 - 1)

### 6. ทดลองกับ byte (ข้อมูล 8 บิต) และ sizeof():

__วัตถุประสงค์__

ทำความเข้าใจการเก็บค่าจำนวนเต็มขนาดเล็ก (0-255) และขนาด

- โค้ดที่ต้องเพิ่มใน `setup()` โดยลบโค้ดเดิมออก ยกเว้นบรรทัด `Serial.begin(115200);`


``` C++
Serial.println("\n--- ทดสอบชนิดข้อมูล byte ---");
Serial.print("ขนาดของ byte: ");
Serial.print(sizeof(byte));
Serial.println(" bytes");

byte myByte = 250;
Serial.print("myByte = 250 ผลลัพธ์: ");
Serial.println(myByte);

myByte = 256; // ค่าเกินขอบเขต (0-255)
Serial.print("myByte = 256 ผลลัพธ์: ");
Serial.println(myByte); // สังเกตผลลัพธ์
```
- ดำเนินการจำลองตามวิธีที่ได้ทดลองมาแล้ว

__คำถาม__

byte ใช้กี่ไบต์? เมื่อ myByte ถูกกำหนดให้เป็น 256 ผลลัพธ์ที่ได้คืออะไร และเพราะเหตุใด?

    byte ใช้ 1 ไบต์ (1 byte)
    ยืนยันได้จากผลของ sizeof(byte) ที่แสดงค่า 1
    byte บน Arduino/ESP32 คือข้อมูล 8 บิต (0-255)

## ส่วนที่ 2: การคำนวณและแปลงชนิดข้อมูลเบื้องต้น

     ผลลัพธ์:
      myByte = 256; แล้ว Serial.println(myByte); จะแสดงผลเป็น 0
      ยืนยันได้จากผลของ sizeof(byte) ที่แสดงค่า 
      byte บน Arduino/ESP32 คือข้อมูล 8 บิต (0-255)

### 7. การคำนวณด้วยชนิดข้อมูลต่างกัน (Type Promotion)

__วัตถุประสงค์__

- ทำความเข้าใจว่าการคำนวณระหว่างชนิดข้อมูลต่างกันมีผลลัพธ์อย่างไร

- โค้ดที่ต้องเพิ่มใน `setup()` โดยลบโค้ดเดิมออก ยกเว้นบรรทัด `Serial.begin(115200);`

``` C++
Serial.println("\n--- ทดสอบการคำนวณระหว่างชนิดข้อมูล ---");
int numA = 10;
float numB = 3.0;
double numC = 3.0;

float resultFloat = numA / numB; // int หาร float ผลลัพธ์จะเป็น float
Serial.print("10 / 3.0 (ผลลัพธ์ float): ");
Serial.println(resultFloat, 2);

double resultDouble = numA / numC; // int หาร double ผลลัพธ์จะเป็น double
Serial.print("10 / 3.0 (ผลลัพธ์ double): ");
Serial.println(resultDouble, 10);

int resultInt = numA / (int)numB; // int หาร int ผลลัพธ์จะเป็น int (ทศนิยมถูกตัดทิ้ง)
Serial.print("10 / (int)3.0 (ผลลัพธ์ int): ");
Serial.println(resultInt);
```

- ดำเนินการจำลองตามวิธีที่ได้ทดลองมาแล้ว

__คำถาม__ 

ทำไม 10 / 3.0 (เมื่อตัวหารเป็น float หรือ double) ถึงได้ผลลัพธ์เป็นทศนิยม แต่เมื่อตัวหารถูกแปลงเป็น int แล้วผลลัพธ์เป็นจำนวนเต็ม?

    ชนิดข้อมูลที่เกี่ยวข้องกับการคำนวณ
    ถ้าฝั่งใดฝั่งหนึ่ง (ตัวตั้งหรือตัวหาร) เป็น float หรือ double
    ผลลัพธ์จะถูก “เลื่อนขั้น” (Type Promotion) เป็น float หรือ double ตามชนิดที่กว้างกว่า
    ถ้าทั้งสองฝั่งเป็น int
    ผลลัพธ์จะเป็น int (เลขทศนิยมถูกตัดทิ้ง)

### 8. การแปลงชนิดข้อมูล (Explicit Type Casting):

__วัตถุประสงค์__ 

-ฝึกการแปลงชนิดข้อมูลด้วยตัวเอง

- โค้ดที่ต้องเพิ่มใน `setup()` โดยลบโค้ดเดิมออก ยกเว้นบรรทัด `Serial.begin(115200);`

``` C++
Serial.println("\n--- ทดสอบการแปลงชนิดข้อมูล (Type Casting) ---");
float temperatureCelsius = 25.789;
Serial.print("อุณหภูมิ Celsius (float): ");
Serial.println(temperatureCelsius);

int temperatureInteger = (int)temperatureCelsius; // แปลง float เป็น int
Serial.print("อุณหภูมิ Celsius (แปลงเป็น int): ");
Serial.println(temperatureInteger);

// ตัวอย่างการใช้ char เป็นตัวเลข
char letter = 'P';
int asciiValue = (int)letter;
Serial.print("อักขระ 'P' มีค่า ASCII: ");
Serial.println(asciiValue);

// ตัวอย่างการแปลง int เป็น float
int count = 7;
int total = 20;
float average = (float)count / total; // ต้องแปลงอย่างน้อยหนึ่งตัวให้เป็น float ก่อนหาร
Serial.print("ค่าเฉลี่ย (7/20): ");
Serial.println(average);
```
- ดำเนินการจำลองตามวิธีที่ได้ทดลองมาแล้ว

__คำถาม__ 

นักศึกษาจะใช้การทำ Type Casting ในสถานการณ์ใดบ้างในการเขียนโปรแกรม?

    เมื่อแปลงชนิดข้อมูลเพื่อให้คำนวณได้ผลลัพธ์ถูกต้อง
    แปลงข้อมูลเพื่อนำไปใช้งานกับฟังก์ชันหรือ library ที่ต้องการชนิดข้อมูลเฉพาะ
    แปลงค่าทศนิยมเป็นจำนวนเต็ม (เช่น ตัดทศนิยมออก หรือปัดเศษ)
    แปลง char เป็น int เพื่อดูรหัส ASCII หรือในทางกลับกัน
    ป้องกัน overflow/underflow หรือแปลงชนิดข้อมูลที่เหมาะสมกับขนาดข้อมูล
    แปลงชนิดข้อมูลขณะทำงานกับ Sensor หรือข้อมูลจาก Serial
