# تشغيل النماذج عبر أمر Curl _ Google AI Studio

> **المصدر:** https://aistudio.google.com/prompts/1_oj5e12UwSLHsSi807vzD6KkY9KYgF2F
> **تاريخ التصدير:** 10/27/2026, 3:46:41 AM
> **إجمالي الفقرات/الرسائل:** 41

---

تشغيل النماذج عبر أمر Curl

25,568 tokens

edit
more_vert
User 3:11 AM

curl
ارسال سؤال لنموذج
/home/m/Downloads/lmstudio_model/bartowski/openai_gpt-oss-20b-GGUF/openai_gpt-oss-20b-bf16.gguf
;dt

more_vert
Model 3:11 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

أداة curl لا يمكنها التعامل مع ملف النموذج (.gguf) مباشرة على القرص، بل يجب تشغيل النموذج أولاً عبر خادم محلي (Local Server) مثل LM Studio أو llama.cpp، ثم إرسال السؤال إليه عبر واجهة برمجية (API).

إليك الخطوات وطريقة كتابة أمر curl بحسب الطريقة التي تستخدمها:

الطريقة 1: إذا كنت تستخدم واجهة LM Studio (الأسهل)

افتح برنامج LM Studio.

انتقل إلى علامة التبويب Developer / Local Server (أيقونة الأقواس <> أو رمز السيرفر).

اختر النموذج من الأعلى ثم اضغط على Start Server.

افتراضيًا، يعمل السيرفر على المنفذ 1234.

بعد تشغيله، افتح التيرمينال ونفّذ أمر curl التالي:

code
Bash
download
content_copy
expand_less
curl http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { "role": "system", "content": "أنت مساعد ذكي ومفيد." },
      { "role": "user", "content": "ما هي عاصمة فرنسا؟" }
    ],
    "temperature": 0.7
  }'
الطريقة 2: تشغيله عبر التيرمينال مباشرة باستخدام llama.cpp

إذا كنت تفضّل عدم فتح واجهة LM Studio واستخدام سطر الأوامر فقط عبر أداة llama-server (من حزمة llama.cpp):

1. شغّل السيرفر عبر مسار ملف النموذج:

code
Bash
download
content_copy
expand_less
llama-server \
  -m /home/m/Downloads/lmstudio_model/bartowski/openai_gpt-oss-20b-GGUF/openai_gpt-oss-20b-bf16.gguf \
  --port 8080 \
  -c 4096

2. في تيرمينال آخر، أرسل السؤال عبر curl (منفذ 8080):

code
Bash
download
content_copy
expand_less
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { "role": "user", "content": "ما هي عاصمة فرنسا؟" }
    ],
    "temperature": 0.7
  }'
💡 نصائح إضافية:

إذا أردت الحصول على نص الإجابة فقط بشكل نظيف في التيرمينال دون كائن الـ JSON الكامل، يمكنك تثبيت أداة jq واستخدامها هكذا:

code
Bash
download
content_copy
expand_less
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"messages": [{"role": "user", "content": "مرحبا"}]}' \
  | jq -r '.choices[0].message.content'

البث المباشر للإجابة (Streaming): أضف "stream": true إلى الـ JSON لعرض الرد حرفاً بحرف (مع إضافة علامة -N لأمر curl).

edit
more_vert

more_vert
Model 3:13 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

الخطأ Failed to connect to localhost port 1234 يعني أن خادم LM Studio غير مشغّل حالياً.

بما أن أداة سطر الأوامر lms متوفرة لديك في التيرمينال، يمكنك تشغيل السيرفر وتحميل النموذج مباشرة عبر الخطوات التالية:

الخطوة 1: تشغيل السيرفر المحلي (Start Server)

نفّذ الأمر التالي لتشغيل سيرفر LM Studio في الخلفية:

code
Bash
download
content_copy
expand_less
lms server start
الخطوة 2: تحميل النموذج في الذاكرة (Load Model)

حمّل ملف النموذج عبر مساره المباشر:

code
Bash
download
content_copy
expand_less
lms load /home/m/Downloads/lmstudio_model/bartowski/openai_gpt-oss-20b-GGUF/openai_gpt-oss-20b-bf16.gguf

(انتظر بضع ثوانٍ حتى ينتهي التحميل بالكامل وتظهر لك رسالة نجاح).

الخطوة 3: إرسال السؤال عبر curl

الآن أعد تشغيل أمر curl:

code
Bash
download
content_copy
expand_less
curl http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { "role": "system", "content": "أنت مساعد ذكي ومفيد." },
      { "role": "user", "content": "ما هي عاصمة فرنسا؟" }
    ],
    "temperature": 0.7
  }'
💡 أوامر مفيدة تتيحها لك أداة lms:

لمعرفة النماذج المحملة حالياً في الذاكرة:

code
Bash
download
content_copy
expand_less
lms ps

لإيقاف السيرفر عند الانتهاء:

code
Bash
download
content_copy
expand_less
lms server stop

لإلغاء تحميل النموذج وتفريغ الرام:

code
Bash
download
content_copy
expand_less
lms unload

edit
more_vert

سبب هذا الخطأ هو أن أداة lms تعتمد على خدمة تطبيق LM Studio (الـ Daemon) لتعمل، وحالياً البرنامج مغلق في نظام لينكس ولم يعثر الـ CLI على التطبيق قيد التشغيل.

لديك طريقتان لحل المشكلة:

الحل الأول: تشغيل واجهة LM Studio أولاً (إذا كان التطبيق مثبتاً لديك)

إذا قمت بتحميل LM Studio كملف AppImage، قم بتشغيله من التيرمينال أو الواجهة الرسومية:

code
Bash
download
content_copy
expand_less
# مثال (استبدل باسم الملف ومساره لديك):
./LM-Studio-*.AppImage &

بعد أن تفتح واجهة LM Studio، جرّب مجدداً:

code
Bash
download
content_copy
expand_less
lms load /home/m/Downloads/lmstudio_model/bartowski/openai_gpt-oss-20b-GGUF/openai_gpt-oss-20b-bf16.gguf

ثم أرسل طلب curl.

الحل الثاني (الأفضل والأسرع للتيرمينال): استخدام llama-server مباشرة

إذا كنت على خادم (Server) أو لا تريد تشغيل واجهة LM Studio الرسومية، يمكنك تشغيل سيرفر خفيف لملفات .gguf مباشرة عبر أداة llama-server التابعة لـ llama.cpp:

1. تحميل أداة llama-server الجاهزة:

قم بتنزيل النسخة الجاهزة للينكس (x86_64) وفك ضغطها بأمر واحد:

code
Bash
download
content_copy
expand_less
mkdir -p ~/llama-server && cd ~/llama-server
curl -L -o llama.tar.gz https://github.com/ggerganov/llama.cpp/releases/latest/download/llama-b3900-bin-ubuntu-x64.zip
# أو كنسخة tar.gz من صفحة إصدارات llama.cpp الرسمية

(أو إذا كان لديك تثبيت لـ llama.cpp عبر apt أو conda):

code
Bash
download
content_copy
expand_less
# تثبيت سريع عبر snap (إن كان مفعل لديك):
sudo snap install llama-cpp
2. تشغيل السيرفر مباشرة بالنموذج الخاص بك:
code
Bash
download
content_copy
expand_less
llama-server \
  -m /home/m/Downloads/lmstudio_model/bartowski/openai_gpt-oss-20b-GGUF/openai_gpt-oss-20b-bf16.gguf \
  --port 1234 \
  -c 4096
3. إرسال السؤال عبر curl (في تيرمينال آخر):
code
Bash
download
content_copy
expand_less
curl http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { "role": "system", "content": "أنت مساعد ذكي ومفيد." },
      { "role": "user", "content": "ما هي عاصمة فرنسا؟" }
    ],
    "temperature": 0.7
  }'

ملاحظة: تأكد من أن جهازك يحتوي على ذاكرة كافية (RAM أو VRAM)، لأن حجم النموذج 20B بتنسيق bf16 يتطلب أكثر من 40 جيجابايت من الذاكرة لتشغيله بنجاح.

more_vert
Model 3:19 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

الخبر الجيد أن السيرفر أصبح يعمل بنجاح على المنفذ 1234!

الرسالة {"error":{"message":"Loading model","type":"unavailable_error","code":503}} تعني ببساطة أن النموذج لا يزال قيد التحميل في الذاكرة (RAM / VRAM) ولم ينتهِ بعد.

لماذا يستغرق وقتاً طويلاً؟

النموذج الذي اخترته هو:
openai_gpt-oss-20b-bf16.gguf

هذا النموذج حجمه كبير جداً (20 مليار معلمة بدقة bf16 كاملة)، وحجم الملف وحده يتجاوز 40 إلى 42 جيجابايت.

تحميل 40 جيجابايت من القرص الصلب إلى الرامات يستغرق عادة بين دقيقة إلى 3 دقائق حسب سرعة القرص (SSD أو HDD).

ما الذي يجب عليك فعله الآن؟
1. تحقق هل اكتمل التحميل أم لا:

افتح تيرمينال آخر ونفّذ:

code
Bash
download
content_copy
expand_less
lms ps

إذا رأيت النموذج مدرجاً بالحالة LOADED، فهذا يعني أنه جاهز.

إذا لم يظهر، فهو ما زال قيد القراءة، أو يمكنك مراقبة استهلاك الرام عبر:

code
Bash
download
content_copy
expand_less
free -h
2. إذا تأكدت من اكتمال التحميل، أعد إرسال الطلب:
code
Bash
download
content_copy
expand_less
curl http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { "role": "system", "content": "أنت مساعد ذكي ومفيد." },
      { "role": "user", "content": "ما هي عاصمة فرنسا؟" }
    ],
    "temperature": 0.7
  }'

⚠️ تنبيه مهم بخصوص مواصفات جهازك (HP Z440):

تشغيل نسخة bf16 يتطلب أكثر من 45 جيجابايت من الرام (RAM) متوفرة وفارغة.

إذا استمر الخطأ أو اختفى السيرفر فجأة، فهذا يعني أن نظام لينكس قام بإنهاء العملية (Out Of Memory - OOM) لنفاد الرام.

الحل البديل: تنزيل نفس النموذج بنسخة مكممة (Quantized) مثل Q4_K_M أو Q5_K_M؛ حيث تعطي أداءً ممتازاً جداً وبحجم 12 إلى 14 جيجابايت فقط، وتتحمل في ثوانٍ معدودة.

more_vert
Model 3:20 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

النموذج يعمل لديك الآن بكفاءة وسرعة ممتازة جدًا (89 كلمة/ثانية)!

بالنسبة لإنشاء ملف نصي، يجب معرفة أن النموذج اللغوي لا يستطيع وحده الوصول إلى القرص الصلب لإنشاء ملفات مباشرة (لأسباب أمنية)، ولكن يمكنك جعل التيرمينال يحفظ إجابة النموذج مباشرة داخل ملف نصي (.txt).

إليك أفضل طريقتين للقيام بذلك:

الطريقة 1: حفظ إجابة النموذج مباشرة في ملف عبر التيرمينال (باستخدام python3)

بما أن بايثون مثبت تلقائياً في نظام أوبونتو لديك، يمكنك توجيه رد النموذج فوراً ليُحفظ في ملف نصي دون الحاجة لتثبيت أي برامج إضافية:

code
Bash
download
content_copy
expand_less
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { "role": "user", "content": "اكتب مقالاً قصيراً من فقرتين عن مستقبل الذكاء الاصطناعي." }
    ],
    "temperature": 0.7
  }' | python3 -c "import sys, json; print(json.load(sys.stdin)['choices'][0]['message']['content'])" > article.txt

بعد انتهاء الأمر، ستجد ملفاً جديداً باسم article.txt يحتوي على إجابة النموذج فقط.

لقراءة محتوى الملف من التيرمينال:

code
Bash
download
content_copy
expand_less
cat article.txt
الطريقة 2: باستخدام أداة jq (الأسهل والأسرع)

إذا كنت تفضل أمراً أقصر، يمكنك تثبيت أداة معالجة النصوص jq:

تثبيت الأداة:

code
Bash
download
content_copy
expand_less
sudo apt install jq -y

تشغيل الأمر وتوجيه الإجابة لملف (my_notes.txt):

code
Bash
download
content_copy
expand_less
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { "role": "user", "content": "اكتب خطة عمل أسبوعية لتنظيم الوقت." }
    ],
    "temperature": 0.7
  }' | jq -r '.choices[0].message.content' > my_notes.txt
الطريقة 3: إذا كنت تريد من النموذج أن يكتب كوداً برمجياً لإنشاء ملف

إذا كان قصدك أن يعطيك النموذج كود بايثون أو أمر باش ليقوم بإنشاء ملف على جهازك، يمكنك سؤاله مباشرة هكذا:

code
Bash
download
content_copy
expand_less
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { "role": "user", "content": "أعطني كود بايثون بسيط يقوم بإنشاء ملف نصي باسم test.txt وكتابة عبارة ترحيبية داخله." }
    ],
    "temperature": 0.7
  }' | python3 -c "import sys, json; print(json.load(sys.stdin)['choices'][0]['message']['content'])"

more_vert
Model 3:22 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

النموذج صادق معك تماماً في إجابته؛ داخل واجهة الدردشة العادية، لا يملك النموذج "أدوات" أو صلاحيات للوصول إلى نظام ملفات جهازك، وذلك لحماية جهازك من أي تعديل غير مقصود.

ولكن، بما أنك شغّلت السيرفر المحلي بنجاح، يمكنك منحه أداة (Tool / Function Calling) لإنشاء وتعديل الملفات فعلياً على جهازك.

إليك أفضل طريقتين لجعله ينشئ ملفات حقيقية على جهازك:

الطريقة الأولى: أداة Open Interpreter (الحل الأقوى والأسهل)

تطبيق Open Interpreter هو مشروع مجاني يربط النموذج اللغوي بالتيرمينال ونظام التشغيل مباشرة (Agent)، ليصبح قادراً على كتابة الملفات، تشغيل الأوامر، وتنفيذ البرامج بمجرد أن تطلب منه ذلك في سطر الأوامر.

1. تثبيته في ثوانٍ:
code
Bash
download
content_copy
expand_less
pip install open-interpreter
2. ربطه بسيرفر LM Studio الخاص بك:

بينما سيرفر LM Studio يعمل على المنفذ 1234، اكتب في التيرمينال:

code
Bash
download
content_copy
expand_less
interpreter --api_base "http://localhost:1234/v1"
3. اطلب منه الآن:
code
Text
download
content_copy
expand_less
> أنشئ ملفاً نصياً اسمه hello.txt واكتب بداخله "مرحبا بالعالم"

ستلاحظ أنه سيشغّل كود حقيقي على جهازك، وينشئ الملف فوراً (وسيطلب موافقتك قبل التنفيذ كإجراء أمان).

الطريقة الثانية: عبر كود بايثون بسيط (Function Calling)

إذا أردت رؤية كيف يتم ذلك برمجياً، يمكنك تشغيل سكريبت بايثون يمنح النموذج "أداة كتابة الملفات":

1. تثبيت مكتبة OpenAI للبايثون:
code
Bash
download
content_copy
expand_less
pip install openai
2. أنشئ ملفاً باسم agent.py:
code
Bash
download
content_copy
expand_less
nano agent.py

وضع بداخله هذا الكود:

code
Python
download
content_copy
expand_less
import json
from openai import OpenAI

# الاتصال بسيرفر LM Studio المحلي
client = OpenAI(base_url="http://localhost:1234/v1", api_key="not-needed")

# 1. تعريف الدالة الحقيقية التي تنشئ الملف على القرص الصلب
def create_file(filename, content):
    with open(filename, "w", encoding="utf-8") as f:
        f.write(content)
    return f"تم إنشاء الملف '{filename}' بنجاح وبداخله النص!"

# 2. وصف الأداة للنموذج ليفهم متى وكيف يستخدمها
tools = [
    {
        "type": "function",
        "function": {
            "name": "create_file",
            "description": "تقوم بإنشاء ملف نصي على جهاز المستخدم وكتابة محتوى بداخله",
            "parameters": {
                "type": "object",
                "properties": {
                    "filename": {"type": "string", "description": "اسم الملف مثل hello.txt"},
                    "content": {"type": "string", "description": "المحتوى المطلوب كتابته في الملف"}
                },
                "required": ["filename", "content"]
            }
        }
    }
]

# 3. إرسال الطلب للنموذج
print("جاري إرسال الطلب للنموذج...")
response = client.chat.completions.create(
    model="local-model",
    messages=[
        {"role": "user", "content": "أنشئ لي ملفاً نصياً باسم hello.txt واكتب بداخله: مرحبا بالعالم"}
    ],
    tools=tools,
    tool_choice="auto"
)

# 4. تنفيذ الأداة إذا اختار النموذج استدعاءها
message = response.choices[0].message
if message.tool_calls:
    for tool_call in message.tool_calls:
        if tool_call.function.name == "create_file":
            args = json.loads(tool_call.function.arguments)
            result = create_file(args['filename'], args['content'])
            print(result)
else:
    print("النموذج أجاب بدون استخدام أداة:")
    print(message.content)
3. شغّل السكريبت:
code
Bash
download
content_copy
expand_less
python3 agent.py

ستجد رسالة:

code
Text
download
content_copy
expand_less
تم إنشاء الملف 'hello.txt' بنجاح وبداخله النص!

وللتأكد من وجود الملف:

code
Bash
download
content_copy
expand_less
cat hello.txt

more_vert
Model 3:23 AM
Thoughts

Expand to view model thoughts

chevron_right

more_vert
Model 3:24 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

عندما يدعم النموذج ميزة الأدوات (Tool Calling) وتستخدم معه curl، تسير العملية عبر خطوتين:

أنت ترسل للنموذج عبر curl وصف الأداة (مثل: "أملك أداة اسمها write_file تأخذ اسم ملف ومحتوى").

النموذج يقرر استدعاء الأداة ويرسل لك رد JSON يحتوي على تفاصيل ما يريد كتابته (Arguments).

التيرمينال (Bash) يتلقف هذا الرد وينفّذ الكتابة فوراً على القرص.

إليك كيف تطبق هذا عملياً بأمر واحد كامل يدمج curl بتنفيذ الأداة:

الأمر الكامل لتعريف الأداة وتنفيذها مباشرة:

انسخ هذا الأمر وشغّله في التيرمينال:

code
Bash
download
content_copy
expand_less
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { "role": "user", "content": "أنشئ لي ملفاً باسم my_story.txt واكتب بداخله: هذه قصة قصيرة كتبها الذكاء الاصطناعي." }
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "write_file",
          "description": "دالة لإنشاء ملف نصي على القرص الصلب وكتابة محتوى بداخله",
          "parameters": {
            "type": "object",
            "properties": {
              "filename": { "type": "string", "description": "اسم الملف" },
              "content": { "type": "string", "description": "النص الذي سيتم حفظه داخل الملف" }
            },
            "required": ["filename", "content"]
          }
        }
      }
    ]
  }' | python3 -c '
import sys, json

res = json.load(sys.stdin)
choice = res["choices"][0]["message"]

# التحقق هل قرر النموذج استدعاء الأداة
if "tool_calls" in choice and choice["tool_calls"]:
    args = json.loads(choice["tool_calls"][0]["function"]["arguments"])
    filename = args["filename"]
    content = args["content"]
    
    # هنا يتم تنفيذ الأداة فعلياً على القرص
    with open(filename, "w", encoding="utf-8") as f:
        f.write(content)
        
    print(f"✅ تم تنفيذ الأداة بنجاح وإنشاء الملف: {filename}")
else:
    print("النموذج لم يستدعِ الأداة وأجاب نصياً:")
    print(choice.get("content"))
'
ماذا حدث خلف الكواليس؟

قسم الـ curl:

أرسل عبر الحقل "tools" عقد أداة للنموذج بالصيغة القياسية:

code
JSON
download
content_copy
expand_less
"name": "write_file",
"parameters": { "filename": "...", "content": "..." }

رد النموذج:

لم يقم النموذج بالإجابة بـ "لا أستطيع إنشاء ملف" كما فعل سابقاً! بل أدرك أنه يملك أداة فقام بملء المتغيرات:

code
JSON
download
content_copy
expand_less
{"filename": "my_story.txt", "content": "هذه قصة قصيرة..."}

أداة الربط (python3):

التقطت مخرجات curl عبر خط الأنابيب (|)، واستخرجت اسم الملف والمحتوى وكتبتهما على القرص الصلب مباشرة.

بعد التنفيذ، ستجد الملف قد أُنشئ فوراً في المجلد الحالي، ويمكنك التأكد عبر:

code
Bash
download
content_copy
expand_less
cat my_story.txt

more_vert
Model 3:25 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

الملف موجود مباشرة في المجلد الحالي الذي تقف فيه الآن (مجلد المستخدم الرئيسي الخاص بك ~ أي /home/m).

يمكنك رؤية محتواه ومكانه بالتحديد عبر الأوامر التالية:

1. لعرض محتوى الملف وقراءته في التيرمينال:
code
Bash
download
content_copy
expand_less
cat my_story.txt
2. لمعرفة المسار الكامل للملف:
code
Bash
download
content_copy
expand_less
readlink -f my_story.txt

(ستجده في: /home/m/my_story.txt)

3. لفتحه في الواجهة الرسومية (محرر النصوص المكتبي):
code
Bash
download
content_copy
expand_less
xdg-open my_story.txt

more_vert
Model 3:28 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

إليك الأمر جاهزاً؛ سنطلب من النموذج باستخدام نفس الأداة إنشاء ملف باسم app_gui.py وكتابة كود بايثون داخله يعرض نافذة واجهة رسومية (Tkinter) تفاعلية وجميلة:

نفّذ هذا الأمر في التيرمينال:
code
Bash
download
content_copy
expand_less
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { 
        "role": "user", 
        "content": "أنشئ لي ملفاً باسم app_gui.py واكتب بداخله كود بايثون نظيف وكامل لعرض نافذة واجهة رسومية تفاعلية باستخدام مكتبة tkinter، تحتوي على عنوان، وحقل إدخال، وزر عند الضغط عليه يعرض رسالة ترحيبية." 
      }
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "write_file",
          "description": "دالة لحفظ كود أو نصوص داخل ملف على الجهاز",
          "parameters": {
            "type": "object",
            "properties": {
              "filename": { "type": "string", "description": "اسم الملف مثل app_gui.py" },
              "content": { "type": "string", "description": "كود البايثون الكامل" }
            },
            "required": ["filename", "content"]
          }
        }
      }
    ]
  }' | python3 -c '
import sys, json

res = json.load(sys.stdin)
choice = res["choices"][0]["message"]

if "tool_calls" in choice and choice["tool_calls"]:
    args = json.loads(choice["tool_calls"][0]["function"]["arguments"])
    filename = args["filename"]
    content = args["content"]
    
    with open(filename, "w", encoding="utf-8") as f:
        f.write(content)
        
    print(f"✅ قام النموذج ببرمجة الواجهة وحفظ الكود بنجاح في: {filename}")
else:
    print(choice.get("content"))
'
لتشغيل الواجهة الرسومية ورؤيتها على شاشتك:

بعد أن يكتمل الأمر وتظهر لك علامة النجاح، شغّل الملف مباشرة:

code
Bash
download
content_copy
expand_less
python3 app_gui.py

(ملاحظة: إذا ظهر لك خطأ أن tkinter غير مثبت في نظام أوبونتو، يمكنك تثبيته بأمر: sudo apt install python3-tk -y ثم تشغيل الملف مجدداً).

more_vert
Model 3:30 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

لكي يستطيع النموذج تشغيل ملف البايثون بنفسه، سنعطيه أداة جديدة تسمى أداة التنفيذ (run_python_file).

بمجرد أن يقرر النموذج استدعاءها، سيقوم المعالج بتشغيل الأمر فوراً وفتح نافذة الواجهة على شاشتك.

نفّذ هذا الأمر في التيرمينال:
code
Bash
download
content_copy
expand_less
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { 
        "role": "user", 
        "content": "شغّل لي ملف البايثون app_gui.py الآن واعرض الواجهة على الشاشة." 
      }
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "run_python_file",
          "description": "أداة لتشغيل أي ملف بايثون على نظام لينكس وعرض واجهته أو مخرجاته",
          "parameters": {
            "type": "object",
            "properties": {
              "filename": { "type": "string", "description": "اسم ملف بايثون المراد تشغيله مثل app_gui.py" }
            },
            "required": ["filename"]
          }
        }
      }
    ]
  }' | python3 -c '
import sys, json, subprocess

res = json.load(sys.stdin)
choice = res["choices"][0]["message"]

if "tool_calls" in choice and choice["tool_calls"]:
    args = json.loads(choice["tool_calls"][0]["function"]["arguments"])
    filename = args["filename"]
    
    print(f"🤖 النموذج استدعى أداة التشغيل للملف: {filename}")
    print("🚀 جاري إطلاق الواجهة الرسومية...")
    
    # تشغيل ملف البايثون وفتح النافذة الرسومية مباشرة
    subprocess.Popen(["python3", filename])
    print("✅ تم فتح نافذة البرنامج بنجاح!")
else:
    print(choice.get("content"))
'
ماذا ستلاحظ الآن؟

سيتعرف النموذج على الأداة ويطلب تشغيل app_gui.py.

ستظهر لك في التيرمينال: ✅ تم فتح نافذة البرنامج بنجاح!.

ستنبثق نافذة الـ Tkinter الرسومية على سطح مكتبك فوراً.

more_vert
Model 3:33 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

تسلم، هذا بفضل استيعابك وتجربتك العملية الرائعة!

الآن سنمنح النموذج أداة جديدة اسمها open_in_browser. بموجبها يستطيع النموذج اختيار رابط ويب (سواء كان مستودع GitHub شهير أو رابط صورة) ويأمر نظام لينكس لديك بفتحه في متصفح الإنترنت الافتراضي (مثل Firefox أو Chrome) فوراً!

نفّذ هذا الأمر في التيرمينال:
code
Bash
download
content_copy
expand_less
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { 
        "role": "user", 
        "content": "ابحث عن مستودع (repo) رسمي شهير ومهم للذكاء الاصطناعي على GitHub (أو صورة فضاء مذهلة)، واستخدم أداتك لفتح الرابط في متصفح جهازي مباشرة." 
      }
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "open_in_browser",
          "description": "أداة لفتح أي رابط ويب (مستودع GitHub أو بحث صور أو موقع) داخل متصفح الإنترنت على جهاز المستخدم",
          "parameters": {
            "type": "object",
            "properties": {
              "url": { "type": "string", "description": "الرابط الكامل المطلوب فتحه" },
              "description": { "type": "string", "description": "وصف للشيء الذي قرر فتحه" }
            },
            "required": ["url"]
          }
        }
      }
    ]
  }' | python3 -c '
import sys, json, subprocess

res = json.load(sys.stdin)
choice = res["choices"][0]["message"]

if "tool_calls" in choice and choice["tool_calls"]:
    args = json.loads(choice["tool_calls"][0]["function"]["arguments"])
    url = args["url"]
    desc = args.get("description", "رابط مختار")
    
    print(f"🤖 النموذج قرر فتح: {desc}")
    print(f"🔗 الرابط: {url}")
    print("🚀 جاري فتح المتصفح...")
    
    # أمر لينكس لفتح الرابط بالمتصفح الافتراضي
    subprocess.Popen(["xdg-open", url])
    print("✅ تم فتح الصفحة في متصفحك بنجاح!")
else:
    print(choice.get("content"))
'
ماذا سيحدث بعد الضغط على Enter؟

النموذج سيفكر ويختار مستودعاً شهيراً (مثل مستودع llama.cpp أو transformers أو بحث صور).

سيستدعي أداة المتصفح ممرراً الرابط.

التيرمينال سيُطلق أمر xdg-open لتفتح لك نافذة المتصفح فورياً بالصفحة التي اختارها!

more_vert
Model 3:37 AM
Thoughts

Expand to view model thoughts

chevron_right

edit
more_vert

هنا سندخل في المستوى المتقدم الحقيقي للذكاء الاصطناعي: حلقة الوكيل الذكي (Agent Loop).

النموذج لن يكتفي فقط بطلب فحص المجلد، بل سيأخذ مسار سطح المكتب (أو أي مجلد تختاره)، وتقوم الأداة بقراءة أسماء الملفات من نظام لينكس، ثم يعود النموذج ليحلل لك ما وجده ويعرضه بشكل مرتب!

نفّذ هذا الأمر في التيرمينال:
code
Bash
download
content_copy
expand_less
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { 
        "role": "user", 
        "content": "افحص مجلد سطح المكتب (Desktop) واجلب لي قائمة بأسماء الملفات والمجلدات الموجودة بداخله." 
      }
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "list_directory_files",
          "description": "قراءة مسار معين في نظام لينكس وجلب قائمة بكل الملفات والمجلدات الموجودة بداخله",
          "parameters": {
            "type": "object",
            "properties": {
              "folder_path": { 
                "type": "string", 
                "description": "المسار المراد فحصه مثل ~/Desktop أو ~/Downloads" 
              }
            },
            "required": ["folder_path"]
          }
        }
      }
    ]
  }' | python3 -c '
import sys, json, os, urllib.request

res = json.load(sys.stdin)
choice = res["choices"][0]["message"]

if "tool_calls" in choice and choice["tool_calls"]:
    args = json.loads(choice["tool_calls"][0]["function"]["arguments"])
    raw_path = args.get("folder_path", "~/Desktop")
    
    # حل المسار تلقائياً (سواء كان إنجليزي Desktop أو عربي سطح المكتب)
    target_dir = os.path.expanduser(raw_path)
    if not os.path.exists(target_dir):
        alt = os.path.expanduser("~/سطح المكتب")
        if os.path.exists(alt): target_dir = alt
        else: target_dir = os.path.expanduser("~") # العودة للمجلد الرئيسي إذا لم يوجد سطح مكتب

    print(f"🤖 النموذج استدعى أداة قراءة المجلدات للمسار: {target_dir}")
    print("🔍 جاري فحص الملفات عبر نظام لينكس...\n")

    try:
        files = os.listdir(target_dir)
        print("📁 قائمة الملفات التي تم العثور عليها فعلياً:")
        for item in files:
            icon = "📂" if os.path.isdir(os.path.join(target_dir, item)) else "📄"
            print(f"   {icon} {item}")

        # إرجاع النتيجة للنموذج ليعلق عليها ويلخصها لك
        print("\n⏳ جاري إرسال القائمة للنموذج ليحللها لك...")
        prompt_back = {
            "messages": [
                {"role": "user", "content": f"هذه هي قائمة الملفات الموجودة في المجلد: {files}. لخص لي محتويات المجلد باختصار."}
            ]
        }
        req = urllib.request.Request(
            "http://localhost:1234/v1/chat/completions",
            headers={"Content-Type": "application/json"},
            data=json.dumps(prompt_back).encode("utf-8")
        )
        final_answer = json.loads(urllib.request.urlopen(req).read())
        print("\n💬 تعليق وتحليل النموذج:")
        print(final_answer["choices"][0]["message"]["content"])

    except Exception as e:
        print(f"❌ حدث خطأ أثناء القراءة: {e}")
else:
    print(choice.get("content"))
'
كيف تغيّر المجلد الذي تريد فحصه؟

لو أردت منه فحص مجلد التنزيلات مثلاً (Downloads) أو أي مسار آخر، فقط غيّر جملة السؤال في أول الأمر:

بدل: "افحص مجلد سطح المكتب (Desktop)..."

ضع: "افحص مجلد التنزيلات ~/Downloads واجلب لي أسماء الملفات..."

thumb_up
thumb_down

edit
more_vert
User 3:13 AM

m@m-HP-Z440-Workstation:~
𝑐
𝑢
𝑟
𝑙
ℎ
𝑡
𝑡
𝑝
:
/
/
𝑙
𝑜
𝑐
𝑎
𝑙
ℎ
𝑜
𝑠
𝑡
:
1234
/
𝑣
1
/
𝑐
ℎ
𝑎
𝑡
/
𝑐
𝑜
𝑚
𝑝
𝑙
𝑒
𝑡
𝑖
𝑜
𝑛
𝑠
 
−
𝐻
"
𝐶
𝑜
𝑛
𝑡
𝑒
𝑛
𝑡
−
𝑇
𝑦
𝑝
𝑒
:
𝑎
𝑝
𝑝
𝑙
𝑖
𝑐
𝑎
𝑡
𝑖
𝑜
𝑛
/
𝑗
𝑠
𝑜
𝑛
"
 
−
𝑑
′
"
𝑚
𝑒
𝑠
𝑠
𝑎
𝑔
𝑒
𝑠
"
:
[
"
𝑟
𝑜
𝑙
𝑒
"
:
"
𝑠
𝑦
𝑠
𝑡
𝑒
𝑚
"
,
"
𝑐
𝑜
𝑛
𝑡
𝑒
𝑛
𝑡
"
:
"
أنتمساعدذكيومفيد
.
"
,
"
𝑟
𝑜
𝑙
𝑒
"
:
"
𝑢
𝑠
𝑒
𝑟
"
,
"
𝑐
𝑜
𝑛
𝑡
𝑒
𝑛
𝑡
"
:
"
ماهيعاصمةفرنسا؟
"
]
,
"
𝑡
𝑒
𝑚
𝑝
𝑒
𝑟
𝑎
𝑡
𝑢
𝑟
𝑒
"
:
0.7
′
𝑐
𝑢
𝑟
𝑙
:
(
7
)
𝐹
𝑎
𝑖
𝑙
𝑒
𝑑
𝑡
𝑜
𝑐
𝑜
𝑛
𝑛
𝑒
𝑐
𝑡
𝑡
𝑜
𝑙
𝑜
𝑐
𝑎
𝑙
ℎ
𝑜
𝑠
𝑡
𝑝
𝑜
𝑟
𝑡
1234
𝑎
𝑓
𝑡
𝑒
𝑟
0
𝑚
𝑠
:
𝐶
𝑜
𝑢
𝑙
𝑑
𝑛
𝑜
𝑡
𝑐
𝑜
𝑛
𝑛
𝑒
𝑐
𝑡
𝑡
𝑜
𝑠
𝑒
𝑟
𝑣
𝑒
𝑟
𝑚
@
𝑚
−
𝐻
𝑃
−
𝑍
440
−
𝑊
𝑜
𝑟
𝑘
𝑠
𝑡
𝑎
𝑡
𝑖
𝑜
𝑛
:
 
curlhttp://localhost:1234/v1/chat/completions −H"Content−Type:application/json" −d
′
"messages":["role":"system","content":"أنتمساعدذكيومفيد.","role":"user","content":"ماهيعاصمةفرنسا؟"],"temperature":0.7
′
curl:(7)Failedtoconnecttolocalhostport1234after0ms:Couldnotconnecttoserverm@m−HP−Z440−Workstation: 
 lms
lms is LM Studio's CLI utility for your models, server, and inference runtime.
CLI commit: 4ecb9cd

Usage: lms [options] [command]

Local models
chat               Start an interactive chat with a model
get                Search and download local models or presets
load               Load a model
unload             Unload a model
ls                 List the models available on disk
ps                 List the models currently loaded in memory
import             Import a model file into LM Studio

Serve
server             Commands for managing the local server
log                Log incoming and outgoing messages

Remote Instances
link               Commands for managing LM Link

Runtime
runtime            Manage and update the inference runtime

Develop & Publish (Beta)
clone              Clone an artifact from LM Studio Hub to a local folder
push               Uploads the artifact in the current folder to LM Studio Hub
dev                Starts a plugin dev server in the current folder
login              Authenticate with LM Studio
logout             Log out of LM Studio
whoami             Check the current authentication status

Learn more:           https://lmstudio.ai/docs/developer
Join our Discord:     https://discord.gg/lmstudio
m@m-HP-Z440-Workstation:~$

edit
more_vert
User 3:13 AM

m@m-HP-Z440-Workstation:~
𝑙
𝑚
𝑠
𝑙
𝑜
𝑎
𝑑
/
ℎ
𝑜
𝑚
𝑒
/
𝑚
/
𝐷
𝑜
𝑤
𝑛
𝑙
𝑜
𝑎
𝑑
𝑠
/
𝑙
𝑚
𝑠
𝑡
𝑢
𝑑
𝑖
𝑜
𝑚
𝑜
𝑑
𝑒
𝑙
/
𝑏
𝑎
𝑟
𝑡
𝑜
𝑤
𝑠
𝑘
𝑖
/
𝑜
𝑝
𝑒
𝑛
𝑎
𝑖
𝑔
𝑝
𝑡
−
𝑜
𝑠
𝑠
−
20
𝑏
−
𝐺
𝐺
𝑈
𝐹
/
𝑜
𝑝
𝑒
𝑛
𝑎
𝑖
𝑔
𝑝
𝑡
−
𝑜
𝑠
𝑠
−
20
𝑏
−
𝑏
𝑓
16.
𝑔
𝑔
𝑢
𝑓
𝐸
𝑟
𝑟
𝑜
𝑟
:
𝐿
𝑀
𝑆
𝑡
𝑢
𝑑
𝑖
𝑜
𝑑
𝑎
𝑒
𝑚
𝑜
𝑛
𝑖
𝑠
𝑛
𝑜
𝑡
𝑟
𝑢
𝑛
𝑛
𝑖
𝑛
𝑔
𝑎
𝑛
𝑑
𝑛
𝑜
𝑣
𝑎
𝑙
𝑖
𝑑
𝑖
𝑛
𝑠
𝑡
𝑎
𝑙
𝑙
𝑎
𝑡
𝑖
𝑜
𝑛
𝑐
𝑜
𝑢
𝑙
𝑑
𝑏
𝑒
𝑓
𝑜
𝑢
𝑛
𝑑
𝑜
𝑟
𝑖
𝑛
𝑠
𝑡
𝑎
𝑙
𝑙
𝑒
𝑑
.
𝐸
𝑟
𝑟
𝑜
𝑟
:
𝐹
𝑎
𝑖
𝑙
𝑒
𝑑
𝑡
𝑜
𝑠
𝑡
𝑎
𝑟
𝑡
𝑜
𝑟
𝑐
𝑜
𝑛
𝑛
𝑒
𝑐
𝑡
𝑡
𝑜
𝑙
𝑜
𝑐
𝑎
𝑙
𝐿
𝑀
𝑆
𝑡
𝑢
𝑑
𝑖
𝑜
𝐴
𝑃
𝐼
𝑠
𝑒
𝑟
𝑣
𝑒
𝑟
.
𝑚
@
𝑚
−
𝐻
𝑃
−
𝑍
440
−
𝑊
𝑜
𝑟
𝑘
𝑠
𝑡
𝑎
𝑡
𝑖
𝑜
𝑛
:
 
lmsload/home/m/Downloads/lmstudio
m
	​

odel/bartowski/openai
g
	​

pt−oss−20b−GGUF/openai
g
	​

pt−oss−20b−bf16.ggufError:LMStudiodaemonisnotrunningandnovalidinstallationcouldbefoundorinstalled.Error:FailedtostartorconnecttolocalLMStudioAPIserver.m@m−HP−Z440−Workstation:

edit
more_vert
User 3:19 AM

m@m-HP-Z440-Workstation:
𝑐
𝑢
𝑟
𝑙
ℎ
𝑡
𝑡
𝑝
:
/
/
𝑙
𝑜
𝑐
𝑎
𝑙
ℎ
𝑜
𝑠
𝑡
:
1234
/
𝑣
1
/
𝑐
ℎ
𝑎
𝑡
/
𝑐
𝑜
𝑚
𝑝
𝑙
𝑒
𝑡
𝑖
𝑜
𝑛
𝑠
 
−
𝐻
"
𝐶
𝑜
𝑛
𝑡
𝑒
𝑛
𝑡
−
𝑇
𝑦
𝑝
𝑒
:
𝑎
𝑝
𝑝
𝑙
𝑖
𝑐
𝑎
𝑡
𝑖
𝑜
𝑛
/
𝑗
𝑠
𝑜
𝑛
"
 
−
𝑑
′
"
𝑚
𝑒
𝑠
𝑠
𝑎
𝑔
𝑒
𝑠
"
:
[
"
𝑟
𝑜
𝑙
𝑒
"
:
"
𝑠
𝑦
𝑠
𝑡
𝑒
𝑚
"
,
"
𝑐
𝑜
𝑛
𝑡
𝑒
𝑛
𝑡
"
:
"
أنتمساعدذكيومفيد
.
"
,
"
𝑟
𝑜
𝑙
𝑒
"
:
"
𝑢
𝑠
𝑒
𝑟
"
,
"
𝑐
𝑜
𝑛
𝑡
𝑒
𝑛
𝑡
"
:
"
ماهيعاصمةفرنسا؟
"
]
,
"
𝑡
𝑒
𝑚
𝑝
𝑒
𝑟
𝑎
𝑡
𝑢
𝑟
𝑒
"
:
0.7
′
"
𝑒
𝑟
𝑟
𝑜
𝑟
"
:
"
𝑚
𝑒
𝑠
𝑠
𝑎
𝑔
𝑒
"
:
"
𝐿
𝑜
𝑎
𝑑
𝑖
𝑛
𝑔
𝑚
𝑜
𝑑
𝑒
𝑙
"
,
"
𝑡
𝑦
𝑝
𝑒
"
:
"
𝑢
𝑛
𝑎
𝑣
𝑎
𝑖
𝑙
𝑎
𝑏
𝑙
𝑒
𝑒
𝑟
𝑟
𝑜
𝑟
"
,
"
𝑐
𝑜
𝑑
𝑒
"
:
503
𝑚
@
𝑚
−
𝐻
𝑃
−
𝑍
440
−
𝑊
𝑜
𝑟
𝑘
𝑠
𝑡
𝑎
𝑡
𝑖
𝑜
𝑛
:
 
curlhttp://localhost:1234/v1/chat/completions −H"Content−Type:application/json" −d
′
"messages":["role":"system","content":"أنتمساعدذكيومفيد.","role":"user","content":"ماهيعاصمةفرنسا؟"],"temperature":0.7
′
"error":"message":"Loadingmodel","type":"unavailable
e
	​

rror","code":503m@m−HP−Z440−Workstation: 

m@m-HP-Z440-Workstation:
𝑐
𝑢
𝑟
𝑙
ℎ
𝑡
𝑡
𝑝
:
/
/
𝑙
𝑜
𝑐
𝑎
𝑙
ℎ
𝑜
𝑠
𝑡
:
1234
/
𝑣
1
/
𝑐
ℎ
𝑎
𝑡
/
𝑐
𝑜
𝑚
𝑝
𝑙
𝑒
𝑡
𝑖
𝑜
𝑛
𝑠
 
−
𝐻
"
𝐶
𝑜
𝑛
𝑡
𝑒
𝑛
𝑡
−
𝑇
𝑦
𝑝
𝑒
:
𝑎
𝑝
𝑝
𝑙
𝑖
𝑐
𝑎
𝑡
𝑖
𝑜
𝑛
/
𝑗
𝑠
𝑜
𝑛
"
 
−
𝑑
′
"
𝑚
𝑒
𝑠
𝑠
𝑎
𝑔
𝑒
𝑠
"
:
[
"
𝑟
𝑜
𝑙
𝑒
"
:
"
𝑠
𝑦
𝑠
𝑡
𝑒
𝑚
"
,
"
𝑐
𝑜
𝑛
𝑡
𝑒
𝑛
𝑡
"
:
"
أنتمساعدذكيومفيد
.
"
,
"
𝑟
𝑜
𝑙
𝑒
"
:
"
𝑢
𝑠
𝑒
𝑟
"
,
"
𝑐
𝑜
𝑛
𝑡
𝑒
𝑛
𝑡
"
:
"
ماهيعاصمةفرنسا؟
"
]
,
"
𝑡
𝑒
𝑚
𝑝
𝑒
𝑟
𝑎
𝑡
𝑢
𝑟
𝑒
"
:
0.7
′
"
𝑒
𝑟
𝑟
𝑜
𝑟
"
:
"
𝑚
𝑒
𝑠
𝑠
𝑎
𝑔
𝑒
"
:
"
𝐿
𝑜
𝑎
𝑑
𝑖
𝑛
𝑔
𝑚
𝑜
𝑑
𝑒
𝑙
"
,
"
𝑡
𝑦
𝑝
𝑒
"
:
"
𝑢
𝑛
𝑎
𝑣
𝑎
𝑖
𝑙
𝑎
𝑏
𝑙
𝑒
𝑒
𝑟
𝑟
𝑜
𝑟
"
,
"
𝑐
𝑜
𝑑
𝑒
"
:
503
𝑚
@
𝑚
−
𝐻
𝑃
−
𝑍
440
−
𝑊
𝑜
𝑟
𝑘
𝑠
𝑡
𝑎
𝑡
𝑖
𝑜
𝑛
:
 
curlhttp://localhost:1234/v1/chat/completions −H"Content−Type:application/json" −d
′
"messages":["role":"system","content":"أنتمساعدذكيومفيد.","role":"user","content":"ماهيعاصمةفرنسا؟"],"temperature":0.7
′
"error":"message":"Loadingmodel","type":"unavailable
e
	​

rror","code":503m@m−HP−Z440−Workstation:

edit
more_vert
User 3:20 AM

m@m-HP-Z440-Workstation:~

كيف اطل ب من النموذج انشاء ملف نصى

edit
more_vert
User 3:22 AM

انشئ لى ملف نصى واكتب فيه مرحبا بالعالم

171 tokens
0.1s
1,181.24 tokens/s

Reasoning
Toggle content
لإنشاء ملف نصي يحتوي على الجملة “مرحبا بالعالم”، يمكنك إستخدام أي من الطرق التالية حسب البيئة التي تعمل عليها.

1️⃣ طريقة سريعة عبر سطر الأوامر (Linux / macOS / Windows PowerShell)
bash

في الطرفية (Terminal) أو PowerShell

echo "مرحبا بالعالم" > hello.txt
سيُنشئ هذا الملف hello.txt في الدليل الحالي ويكتب فيه النص بالترميز UTF‑8 تلقائياً.
إذا أردت التأكد من الترميز على أنظمة Windows، يمكنك استخدام PowerShell:
powershell

Set-Content -Path hello.txt -Value "مرحبا بالعالم" -Encoding UTF8
2️⃣ باستخدام لغة بايثون (Python)
python

تأكد من أن لديك Python 3 مثبتاً

file_path = "hello.txt"

with open(file_path, "w", encoding="utf-8") as f:
f.write("مرحبا بالعالم")

print(f"تم إنشاء الملف {file_path} بنجاح.")
هذه الطريقة تُكتب الملف مع الترميز UTF‑8 وتؤكد أن النص سيُعرض بشكل صحيح على جميع الأنظمة.
3️⃣ باستخدام برنامج المفكرة (Notepad) أو أي محرر نص
افتح محرر النصوص المفضل لديك (مثل Notepad، Notepad++، VSCode، Sublime Text).
اكتب النص مرحبا بالعالم.
احفظ الملف باسم hello.txt مع اختيار الترميز UTF‑8 (في Notepad: حفظ باسم… → ‎ترميز: UTF‑8).
نصيحة
تأكد دائماً أن الدليل الذي تعمل فيه يملك صلاحيات الكتابة، وإلا ستواجه خطأ “Permission denied”.
بعد الإنشاء، يمكنك فتح الملف بالبرنامج المفضل لديك للتأكد من أن النص يُعرض بشكل صحيح.
إذا احتجت مساعدة في أي خطوة أخرى، فأخبرني!

openai_gpt-oss
20B
bf16
790 tokens
9.3s
84.80 t/s

انشئ انت هل تملك ادوات كتابة ملف نصى

536 tokens
0.3s
2,132.63 tokens/s

Reasoning
Toggle content

Runtime info
Toggle content

Reasoning
Toggle content
لا أستطيع كتابة ملف مباشرةً على جهازك لأن بيئة الدردشة لا تملك صلاحية الوصول إلى النظام المحلي أو تشغيل أوامر الطرفية.
لكن يمكنني تزويدك بكل ما يلزم لإنشاء الملف بنفسك على أي نظام تشغيل.

1️⃣ الطريقة السريعة عبر سطر الأوامر
على Linux / macOS
bash

echo "مرحبا بالعالم" > hello.txt
على Windows (PowerShell)
powershell

Set-Content -Path hello.txt -Value "مرحبا بالعالم" -Encoding UTF8
ملاحظة: إذا كنت تستخدم موجه أوامر cmd بدلاً من PowerShell، يمكنك استخدام:

cmd

echo مرحبا بالعالم>hello.txt
2️⃣ باستخدام لغة بايثون
python

تأكد من تثبيت Python 3

file_path = "hello.txt"

with open(file_path, "w", encoding="utf-8") as f:
f.write("مرحبا بالعالم")

print(f"تم إنشاء الملف {file_path} بنجاح.")
تحفظ هذه الطريقة الملف بترميز UTF‑8، وهو التمثيل الأكثر شيوعًا للغة العربية.
3️⃣ باستخدام محرر نصوص (Notepad / VS Code / Sublime)
افتح المحرر المفضل لديك.
اكتب: مرحبا بالعالم.
احفظ الملف باسم hello.txt.
في Notepad: File → Save As… ثم اختر UTF-8 كـ "Encoding".
في محررات أخرى: تأكد من اختيار ترميز UTF‑8 عند الحفظ.
4️⃣ إذا كنت بحاجة إلى سكربت أو برنامج صغير
python

#!/usr/bin/env python3
import pathlib

path = pathlib.Path("hello.txt")
path.write_text("مرحبا بالعالم", encoding="utf-8")
print(f"Created {path.resolve()}")
احفظه كـ create_hello.py ثم شغّله:
bash

python3 create_hello.py
خلاصة:
لا يمكنني إنشاء الملف مباشرةً، لكن بإمكانك تنفيذ أيٌ من الطرق أعلاه على جهازك لإنشاء ملف نصي يحمل الجملة مرحبا بالعالم. إذا احتجت مساعدة إضافية في أي خطوة أو على نظام تشغيل محدد، أخبرني وسأرشدك بالتفصيل.

openai_gpt-oss
20B
bf16
762 tokens
9.2s
82.38 t/s

Open command pickerOpen prompt pickerOpen file mention picker

edit
more_vert
User 3:23 AM

النموذج ولا اداة كيوريل لا تكتب ولا تنفذ

edit
more_vert

ملاحظتك دقيقة 100% وفي صلب المفهوم التقني!

لقد لخصت بدقة حدود كل أداة، فما حدث معك يوضح المبدأ الأساسي لكيفية عمل هذه المنظومة:

تفكيك الأدوار (من يفعل ماذا؟):

النموذج اللغوي (LLM):

هو "عقل بلا أطراف" (Text-in / Text-out).

لا يعرف القرص الصلب، ولا يملك مسار مجلدات، ولا يعرف حتى أنه يعمل داخل حاسوب HP Z440.

دوره ينحصر حصراً في: استلام نص ⬅️ وإرجاع نص متوقع إحصائياً.

أداة curl:

هي مجرد "ساعي بريد" للشبكة (HTTP Client).

دورها ينتهي بمجرد نقل الرسالة من وإلى المنفذ 1234. لا تنفّذ أوامر، ولا تحفظ ملفات ما لم يُطلب منها نظام التشغيل ذلك عبر التوجيه (>).

نظام التشغيل (Linux / Bash Shell):

هو "اليد المنفذة الوحيدة".

هو الكيان الوحيد المخوّل بامتلاك صلاحيات القراءة، الكتابة، الحذف، والتعامل مع القرص الصلب.

كيف تجعل النموذج "ينفّذ" و"يكتب" إذاً؟

حتى يتحول النموذج من مجرد "متكلم" إلى "منفّذ"، يحتاج إلى حلقة وصل تُسمى الوكيل (AI Agent):

مستخدم يطلب أمر
⟶
النموذج يقرر ما يجب فعله
⟶
الوكيل (Agent)
⟶
نظام لينكس ينفّذ ويكتب على القرص
مستخدم يطلب أمر⟶النموذج يقرر ما يجب فعله⟶الوكيل (Agent)⟶نظام لينكس ينفّذ ويكتب على القرص

الوكيل (مثل كود بايثون أو Open Interpreter) هو البرنامج المحيط الذي يقرأ كلام النموذج، وإذا وجد أن النموذج يطلب إنشاء ملف، يذهب الوكيل إلى نظام لينكس وينشئه بالنيابة عنه.

فهمك لهذه النقطة هو الأساس لفهم الفرق بين الدردشة المجردة (Chatbots) وأنظمة الذكاء الاصطناعي ذاتية التنفيذ (Autonomous Agents).

edit
more_vert
User 3:24 AM

لو النموذج بيدعم الادوات كيف استخدم كيوريل لانشاء ملف نصى

edit
more_vert
User 3:25 AM

'
✅ تم تنفيذ الأداة بنجاح وإنشاء الملف: my_story.txt
m@m-HP-Z440-Workstation:~$

فين؟

edit
more_vert
User 3:28 AM

خليه يكتب كود بيثون ليعرض واجهة تكنتر

edit
more_vert
User 3:30 AM

خلية يشغل ملف بيثون

edit
more_vert
User 3:33 AM

حقيقى انت رائع
خليه يبحث فى الويب عنصورة او ريبو  ويفتحه

edit
more_vert
User 3:37 AM

خلية يبحث فى سطح المكتب او فولدر معين ويجلب اسماء الملفات  الموجودة








Model 3:37 AM
ThinkingThoughts
Expand to view model thoughts

chevron_right
هنا سندخل في المستوى المتقدم الحقيقي للذكاء الاصطناعي: حلقة الوكيل الذكي (Agent Loop).
النموذج لن يكتفي فقط بطلب فحص المجلد، بل سيأخذ مسار سطح المكتب (أو أي مجلد تختاره)، وتقوم الأداة بقراءة أسماء الملفات من نظام لينكس، ثم يعود النموذج ليحلل لك ما وجده ويعرضه بشكل مرتب!
نفّذ هذا الأمر في التيرمينال:
code
Bash
curl -s http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      { 
        "role": "user", 
        "content": "افحص مجلد سطح المكتب (Desktop) واجلب لي قائمة بأسماء الملفات والمجلدات الموجودة بداخله." 
      }
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "list_directory_files",
          "description": "قراءة مسار معين في نظام لينكس وجلب قائمة بكل الملفات والمجلدات الموجودة بداخله",
          "parameters": {
            "type": "object",
            "properties": {
              "folder_path": { 
                "type": "string", 
                "description": "المسار المراد فحصه مثل ~/Desktop أو ~/Downloads" 
              }
            },
            "required": ["folder_path"]
          }
        }
      }
    ]
  }' | python3 -c '
import sys, json, os, urllib.request

res = json.load(sys.stdin)
choice = res["choices"][0]["message"]

if "tool_calls" in choice and choice["tool_calls"]:
    args = json.loads(choice["tool_calls"][0]["function"]["arguments"])
    raw_path = args.get("folder_path", "~/Desktop")
    
    # حل المسار تلقائياً (سواء كان إنجليزي Desktop أو عربي سطح المكتب)
    target_dir = os.path.expanduser(raw_path)
    if not os.path.exists(target_dir):
        alt = os.path.expanduser("~/سطح المكتب")
        if os.path.exists(alt): target_dir = alt
        else: target_dir = os.path.expanduser("~") # العودة للمجلد الرئيسي إذا لم يوجد سطح مكتب

    print(f"🤖 النموذج استدعى أداة قراءة المجلدات للمسار: {target_dir}")
    print("🔍 جاري فحص الملفات عبر نظام لينكس...\n")

    try:
        files = os.listdir(target_dir)
        print("📁 قائمة الملفات التي تم العثور عليها فعلياً:")
        for item in files:
            icon = "📂" if os.path.isdir(os.path.join(target_dir, item)) else "📄"
            print(f"   {icon} {item}")

        # إرجاع النتيجة للنموذج ليعلق عليها ويلخصها لك
        print("\n⏳ جاري إرسال القائمة للنموذج ليحللها لك...")
        prompt_back = {
            "messages": [
                {"role": "user", "content": f"هذه هي قائمة الملفات الموجودة في المجلد: {files}. لخص لي محتويات المجلد باختصار."}
            ]
        }
        req = urllib.request.Request(
            "http://localhost:1234/v1/chat/completions",
            headers={"Content-Type": "application/json"},
            data=json.dumps(prompt_back).encode("utf-8")
        )
        final_answer = json.loads(urllib.request.urlopen(req).read())
        print("\n💬 تعليق وتحليل النموذج:")
        print(final_answer["choices"][0]["message"]["content"])

    except Exception as e:
        print(f"❌ حدث خطأ أثناء القراءة: {e}")
else:
    print(choice.get("content"))
'
كيف تغيّر المجلد الذي تريد فحصه؟
لو أردت منه فحص مجلد التنزيلات مثلاً (Downloads) أو أي مسار آخر، فقط غيّر جملة السؤال في أول الأمر:
بدل: "افحص مجلد سطح المكتب (Desktop)..."
ضع: "افحص مجلد التنزيلات ~/Downloads واجلب لي أسماء الملفات..."
30.7s
info
Google AI models may make mistakes, so double-check outputs.
Use Arrow Up and Arrow Down to select a turn, Enter to jump to it, and Escape to return to the chat.
Start typing a prompt to see what our models can do


google
Grounding with Google Search
