📌 وصف المشروع
هذا المشروع يهدف إلى قراءة مجموعة من الحساسات الرقمية والتماثلية باستخدام لوحة Arduino، ويقوم بتحليل البيانات وتشغيل مؤشر LED عند تحقق شروط معينة.
الحساسات المستخدمة:

حساس حركة PIR (رقمي)

زر Push Button (رقمي)

حساس ضوء LDR (تماثلي)

حساس حرارة TMP36 (تماثلي)

LED كمؤشر

🧰 المكونات المطلوبة
المكون	النوع	العدد
Arduino Uno	متحكم	1
PIR Sensor	رقمي	1
Push Button	رقمي	1
LDR	تماثلي	1
مقاومة 10kΩ	مقاومة	1
TMP36	تماثلي	1
LED	مؤشر ضوئي	1
مقاومة 220Ω	مقاومة	1
أسلاك توصيل	-	حسب الحاجة
Breadboard	-	1

🔌 التوصيلات بالتفصيل
1️⃣ توصيل حساس الحركة PIR
VCC → 5V في البردبورد

GND → GND في البردبورد

OUT → Pin D2 في Arduino

2️⃣ توصيل زر التشغيل Push Button
أحد أطراف الزر → GND

الطرف الآخر → Pin D3 في Arduino

يستخدم الكود مقاومة داخلية INPUT_PULLUP فلا حاجة لمقاومة خارجية

3️⃣ توصيل حساس الضوء LDR
طرف LDR الأول → 5V

طرف LDR الثاني → نقطة مشتركة على البردبورد

طرف أول من مقاومة 10kΩ → نفس النقطة المشتركة

الطرف الثاني من المقاومة → GND

النقطة المشتركة → إلى Pin A0 في Arduino (للقراءة التماثلية)

هذا يسمى مقسم جهد (Voltage Divider)

4️⃣ توصيل حساس الحرارة TMP36
من اليسار إلى اليمين (عند قراءة الكتابة على الحساس):

الرجل	التوصيل
1 (يسار)	5V
2 (وسط)	Pin A1
3 (يمين)	GND

5️⃣ توصيل LED
الساق الطويلة (الأنود) → Pin D13 في Arduino

الساق القصيرة (الكاثود) → مقاومة 220Ω → GND

مهم جداً استخدام المقاومة لتجنب تلف الـ LED

💻 كود Arduino

// تعريف الأرجل
const int pirPin = 2;        // حساس الحركة (رقمي)
const int buttonPin = 3;     // زر التشغيل (رقمي)
const int ldrPin = A0;       // حساس الضوء (تماثلي)
const int tempPin = A1;      // حساس الحرارة TMP36 (تماثلي)
const int ledPin = 13;       // مؤشر LED (رقمي)

// متغيرات حالة
bool ledState = false;

void setup() {
  pinMode(pirPin, INPUT);                 // مدخل حساس الحركة
  pinMode(buttonPin, INPUT_PULLUP);       // زر التشغيل بمقاومة داخلية
  pinMode(ledPin, OUTPUT);                // LED كمخرج
  Serial.begin(9600);                     // تفعيل المراقبة التسلسلية
}

void loop() {
  // قراءة الحساسات
  bool motion = digitalRead(pirPin);               // قراءة PIR
  bool buttonPressed = digitalRead(buttonPin) == LOW; // الزر مضغوط؟
  int ldrValue = analogRead(ldrPin);               // قراءة الضوء
  int rawTemp = analogRead(tempPin);               // قراءة TMP36

  // تحويل حرارة TMP36 إلى درجة مئوية
  float voltage = rawTemp * (5.0 / 1023.0);         // تحويل ADC إلى فولت
  float temperatureC = (voltage - 0.5) * 100.0;     // تحويل إلى مئوي

  // طباعة القيم
  Serial.print("حركة: ");
  Serial.print(motion);
  Serial.print(" | زر: ");
  Serial.print(buttonPressed);
  Serial.print(" | ضوء: ");
  Serial.print(ldrValue);
  Serial.print(" | حرارة: ");
  Serial.println(temperatureC);

  // منطق التحكم في LED:
  // شغل الـ LED إذا:
  // - فيه حركة
  // - أو الزر مضغوط
  // - أو الضوء منخفض (القيمة أقل من 300)
  // - أو الحرارة عالية (أعلى من 30°C)
  if (motion || buttonPressed || ldrValue < 300 || temperatureC > 30) {
    digitalWrite(ledPin, HIGH);
    ledState = true;
  } else {
    digitalWrite(ledPin, LOW);
    ledState = false;
  }

  delay(500); // نصف ثانية انتظار
}
📋 ملاحظات عامة
المقاومة 10kΩ مع LDR ضرورية لعمل مقسم الجهد لقراءة صحيحة.

المقاومة 220Ω مع LED تحمي LED من التلف بسبب تيار عالي.

الكود يستخدم المقاومة الداخلية للزر (INPUT_PULLUP) فلا حاجة لمقاومة خارجية.

قيم الإضاءة والحرارة قابلة للتعديل حسب البيئة والاحتياج.

