import tkinter as tk
from tkinter import filedialog, messagebox, simpledialog
from colorama import Fore, Style, init
from datetime import datetime

# تهيئة colorama
init(autoreset=True)

# إعداد نافذة التطبيق
root = tk.Tk()
root.title("إدارة الدخل والمصروفات")
root.geometry("600x400")

# متغيرات الإعدادات
user_name = input("يرجى إدخال اسم المستخدم: ")
user_password = "12345"  # كلمة السر الافتراضية
app_language = 'عربي'  # اللغة الافتراضية
background_color = '#FFFFFF'  # اللون الافتراضي

# قائمة لتخزين الدخل والمصروفات
income_sources = {'راتب': [], 'دروس': []}
expense_sources = {
    'نت': [], 
    'إيجار': [], 
    'بنزين': [], 
    'مصروف المدرسة': [], 
    'مصروف البيت': [], 
    'جمعية': [], 
    'مياه': [], 
    'كهرباء': []
}

# Label لعرض الفرق بين الدخل والمصروفات
difference_label = tk.Label(root, text="", font=("Arial", 14))
difference_label.pack(pady=10)

def add_income(source, amount):
    # إضافة الدخل إلى المصدر المحدد مع التاريخ
    date = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    income_sources[source].append((amount, date))
    update_difference()

def add_expense(source, amount):
    # إضافة المصروف إلى المصدر المحدد مع التاريخ
    date = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    expense_sources[source].append((amount, date))
    update_difference()

def calculate_balance():
    # حساب الرصيد المتبقي
    total_income = sum(amount for source in income_sources for amount, _ in income_sources[source])
    total_expenses = sum(amount for source in expense_sources for amount, _ in expense_sources[source])
    return total_income - total_expenses

def update_difference():
    # تحديث الفرق بين الدخل والمصروفات
    total_income = sum(amount for source in income_sources for amount, _ in income_sources[source])
    total_expenses = sum(amount for source in expense_sources for amount, _ in expense_sources[source])
    difference = total_income - total_expenses
    
    if difference > 0:
        difference_label.config(text=f"فرق الدخل والمصروفات: {difference} (الدخل أكثر)", fg="green")
    elif difference < 0:
        difference_label.config(text=f"فرق الدخل والمصروفات: {-difference} (المصروفات أكثر)", fg="red")
    else:
        difference_label.config(text="لا يوجد فرق بين الدخل والمصروفات", fg="black")

def display_summary():
    # عرض ملخص الدخل والمصروفات
    total_income = sum(amount for source in income_sources for amount, _ in income_sources[source])
    total_expenses = sum(amount for source in expense_sources for amount, _ in expense_sources[source])
    balance = calculate_balance()
    
    summary_text = f"\nمرحبًا، {user_name}! إليك ملخص إدارة الدخل والمصروفات:\n"
    summary_text += f"إجمالي الدخل: {total_income}\n"
    summary_text += f"إجمالي المصروفات: {total_expenses}\n"
    summary_text += f"الرصيد المتبقي: {balance}\n"

    messagebox.showinfo("ملخص الدخل والمصروفات", summary_text)

def add_new_income_source():
    new_source = input("أدخل اسم مصدر الدخل الجديد: ")
    income_sources[new_source] = []
    print(f"تم إضافة مصدر الدخل '{new_source}' بنجاح.")

def add_new_expense_source():
    new_source = input("أدخل اسم مصدر المصروف الجديد: ")
    expense_sources[new_source] = []
    print(f"تم إضافة مصدر المصروف '{new_source}' بنجاح.")

def change_background():
    # تغيير خلفية النافذة
    color = filedialog.askcolor(title="اختر لون الخلفية")[1]
    if color:
        global background_color
        background_color = color
        root.configure(bg=color)

def upload_image():
    # تحميل صورة
    file_path = filedialog.askopenfilename(title="اختر صورة", filetypes=[("Image files", "*.jpg;*.jpeg;*.png")])
    if file_path:
        img = tk.PhotoImage(file=file_path)
        img_label = tk.Label(root, image=img)
        img_label.image = img  # Keep a reference to avoid garbage collection
        img_label.pack()

def open_settings():
    settings_window = tk.Toplevel(root)
    settings_window.title("إعدادات التطبيق")
    settings_window.geometry("300x300")

    # تغيير اسم المستخدم
    def change_user_name():
        global user_name
        new_name = simpledialog.askstring("تغيير اسم المستخدم", "أدخل الاسم الجديد:")
        if new_name:
            user_name = new_name
            messagebox.showinfo("نجاح", "تم تغيير اسم المستخدم بنجاح!")

    # تغيير كلمة المرور
    def change_password():
        global user_password
        new_password = simpledialog.askstring("تغيير كلمة المرور", "أدخل كلمة المرور الجديدة:")
        if new_password:
            user_password = new_password
            messagebox.showinfo("نجاح", "تم تغيير كلمة المرور بنجاح!")

    # تغيير اللغة
    def change_language():
        global app_language
        new_language = simpledialog.askstring("تغيير اللغة", "أدخل اللغة الجديدة:")
        if new_language:
            app_language = new_language
            messagebox.showinfo("نجاح", "تم تغيير اللغة بنجاح!")

    # إضافة الأزرار
    btn_change_user_name = tk.Button(settings_window, text="تغيير اسم المستخدم", command=change_user_name)
    btn_change_user_name.pack(pady=5)

    btn_change_password = tk.Button(settings_window, text="تغيير كلمة المرور", command=change_password)
    btn_change_password.pack(pady=5)

    btn_change_language = tk.Button(settings_window, text="تغيير اللغة", command=change_language)
    btn_change_language.pack(pady=5)

# إعداد الأزرار
btn_display_summary = tk.Button(root, text="عرض الملخص", command=display_summary)
btn_display_summary.pack(pady=10)

btn_add_income = tk.Button(root, text="إضافة دخل جديد", command=add_new_income_source)
btn_add_income.pack(pady=10)

btn_add_expense = tk.Button(root, text="إضافة مصروف جديد", command=add_new_expense_source)
btn_add_expense.pack(pady=10)

btn_change_background = tk.Button(root, text="تغيير لون الخلفية", command=change_background)
btn_change_background.pack(pady=10)

btn_upload_image = tk.Button(root, text="تحميل صورة", command=upload_image)
btn_upload_image.pack(pady=10)

btn_settings = tk.Button(root, text="الإعدادات", command=open_settings)
btn_settings.pack(pady=10)

# بدء التطبيق
root.mainloop()
