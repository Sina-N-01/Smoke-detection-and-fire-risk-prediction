 ساختار کلی پروژه:
نصب YOLOv5

بارگذاری دیتاست (مثلاً Fire Dataset از Kaggle یا گیت‌هاب)

تنظیم ساختار فایل‌ها به سبک YOLOv5

بهبود YOLOv5 برای اهداف کوچک (مثل تغییر anchor ها و افزایش رزولوشن)

آموزش مدل

تست مدل روی تصاویر جدید

نمایش نتایج خروجی

Mine Code:
# 1. نصب YOLOv5 و وابستگی‌ها
!git clone https://github.com/ultralytics/yolov5  # کلون YOLOv5
%cd yolov5
%pip install -r requirements.txt

# 2. دانلود یک دیتاست نمونه آتش‌سوزی جنگل (مثال: Fire Dataset)
!mkdir ../fire_data
%cd ../fire_data
!gdown --id 1Uwiy2RXn1gAViCR0ThEJpp9cMhkpr5Rr --output fire_dataset.zip  # جایگزین با لینک دیتای شما
!unzip fire_dataset.zip

# 3. تنظیم ساختار به فرمت YOLO
# فرض بر این است که فولدرها به صورت datasets/images/train, val و labels/train, val هستند

# 4. ساخت فایل config دیتاست
dataset_yaml = """
train: ../fire_data/dataset/images/train
val: ../fire_data/dataset/images/val

nc: 1
names: ['fire']
"""
with open("/content/yolov5/fire.yaml", "w") as f:
    f.write(dataset_yaml)

# 5. آموزش مدل با پارامترهای بهینه برای اهداف کوچک
%cd /content/yolov5
!python train.py --img 640 --batch 16 --epochs 50 --data fire.yaml --weights yolov5s.pt --name fire-detection --hyp data/hyps/hyp.scratch-low.yaml

# 6. تست مدل آموزش‌دیده روی یک عکس
from IPython.display import Image
!python detect.py --weights runs/train/fire-detection/weights/best.pt --img 640 --conf 0.3 --source ../fire_data/dataset/images/val
Image(filename='runs/detect/exp/image1.jpg')  # جایگزین با اسم تصویر واقعی

# *کات قابل بهبود:
اگر دیتاست مخصوص مقاله‌ات هست و خودت جمع‌آوریش کردی، باید به فرمت YOLO (یعنی [class_id x_center y_center width height]) برگردونید.
!python train.py --img 640 --batch 16 --epochs 50 --data fire.yaml --weights yolov5s.pt --cache --noautoanchor

گام‌های بعدی برای تکمیل پیاده‌سازی مقاله
# 1.بهبود YOLOv5 برای تشخیص اهداف کوچک
در مقاله اصلی معمولاً یکی از این موارد برای بهبود تشخیص اهداف کوچک پیاده‌سازی شده:


# ساختار پوشه YOLOv5 به فرمت استاندارد
ما فرض می‌کنیم دیتاست شامل تصاویر و برچسب‌هاست:
 fire_data/
├── dataset/
│   ├── images/
│   │   ├── train/
│   │   └── val/
│   └── labels/
│       ├── train/
│       └── val/
