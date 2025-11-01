# 🚨 **PHÂN TÍCH NGUYÊN NHÂN VÀ PHƯƠNG ÁN XỬ LÝ**

## 🔍 **NGUYÊN NHÂN CHÍNH XÁC:**

### **1. RawHtml Widget KHÔNG QUA RESOURCE MANAGER**
- RawHtml widget chỉ render HTML thuần túy
- CSS text được hiển thị nhưng **KHÔNG được wrap trong `<style>` tags**
- Không được xử lý bởi OrchardCore Resource Management system

### **2. THIẾU RESOURCE RENDERING TRONG LAYOUT**
- Layout.liquid **PHẢI CÓ** `{% resources type: "Stylesheet" %}` để render CSS
- Nếu thiếu thì CSS sẽ không được inject vào `<head>`

### **3. STATIC FILES KHÔNG ĐƯỢC SERVE ĐÚNG CÁCH**
- File CSS phải đặt trong `~/TheAgencyTheme/wwwroot/` 
- Naming convention: `~/ThemeName/styles/path-to-file.css`
- Cần StaticFileMiddleware để serve files

---

## 📚 **THEO TÀI LIỆU ORCHARDCORE CHÍNH THỨC:**

### **🎯 CÁCH 1: STYLEBLOCK TRONG TEMPLATE (CHUẨN NHẤT)**

**Từ tài liệu:** [OrchardCore Resources - Custom style](https://docs.orchardcore.net/en/latest/reference/modules/Resources/#custom-style)

```liquid
{% styleblock name: "law-firm-custom", depends_on:"bootstrap" %}
    .navbar-brand { 
        color: #d4af37 !important; 
    }
    .btn-primary { 
        background-color: #d4af37 !important; 
        border-color: #d4af37 !important; 
    }
{% endstyleblock %}
```

**Ưu điểm:**
- ✅ Được inject vào `<head>` section tự động
- ✅ Chỉ inject một lần dù template được gọi nhiều lần
- ✅ Có thể set dependencies (depends_on)
- ✅ Được quản lý bởi Resource Manager

---

### **🎯 CÁCH 2: INLINE DEFINITION TRONG TEMPLATE**

**Từ tài liệu:** [OrchardCore Resources - Inline definition](https://docs.orchardcore.net/en/latest/reference/modules/Resources/#inline-definition)

```liquid
{% style name:"law-firm-custom", src:"~/TheAgencyTheme/css/law-firm-custom.css", depends_on:"bootstrap" %}
```

**Yêu cầu:**
- File CSS phải đặt trong `~/TheAgencyTheme/wwwroot/css/law-firm-custom.css`
- Naming convention: `~/ThemeName/styles/path-to-file.css`

---

### **🎯 CÁCH 3: RESOURCE MANIFEST (CHO MODULE/THEME)**

**Từ tài liệu:** [OrchardCore Resources - Registering a Resource Manifest](https://docs.orchardcore.net/en/latest/reference/modules/Resources/#registering-a-resource-manifest)

```csharp
public class ResourceManagementOptionsConfiguration : IConfigureOptions<ResourceManagementOptions>
{
    private static ResourceManifest _manifest;

    static ResourceManagementOptionsConfiguration()
    {
        _manifest = new ResourceManifest();

        _manifest
            .DefineStyle("LawFirm-Custom")
            .SetUrl("~/TheAgencyTheme/css/law-firm-custom.css")
            .SetDependencies("bootstrap")
            .SetVersion("1.0");
    }

    public void Configure(ResourceManagementOptions options)
    {
        options.ResourceManifests.Add(_manifest);
    }
}
```

**Sử dụng:**
```liquid
{% style name:"LawFirm-Custom" %}
```

---

### **🎯 CÁCH 4: RENDERING TRONG LAYOUT**

**Từ tài liệu:** [OrchardCore Resources - Rendering](https://docs.orchardcore.net/en/latest/reference/modules/Resources/#rendering)

**Layout.liquid phải có:**
```liquid
<head>
    ...
    {% resources type: "Meta" %}
    {% resources type: "HeadLink" %}
    {% resources type: "HeadScript" %}
    {% resources type: "Stylesheet" %}
</head>
```

---

---

## 🎯 **3 PHƯƠNG ÁN XỬ LÝ THEO THỨ TỰ ƯU TIÊN:**

### **PHƯƠNG ÁN 1: STYLEBLOCK (KHUYẾN NGHỊ)**
**Ưu điểm:** Đơn giản, không cần file riêng, hoạt động ngay
```liquid
{% styleblock name: "law-firm-custom" %}
.navbar-brand { 
    color: #d4af37 !important; 
}
.btn-primary { 
    background-color: #d4af37 !important; 
    border-color: #d4af37 !important; 
}
{% endstyleblock %}
```

### **PHƯƠNG ÁN 2: INLINE DEFINITION**
**Ưu điểm:** Sử dụng file CSS riêng, có thể cache
```liquid
{% style name:"law-firm-custom", src:"~/TheAgencyTheme/css/law-firm-custom.css" %}
```
**Yêu cầu:** Tạo file `/TheAgencyTheme/wwwroot/css/law-firm-custom.css`

### **PHƯƠNG ÁN 3: RESOURCE MANIFEST**
**Ưu điểm:** Chuyên nghiệp, có dependencies, versioning
**Yêu cầu:** Code C# trong theme/module

---

## 🔧 **HÀNH ĐỘNG CẦN THỰC HIỆN:**

### **BƯỚC 1: KIỂM TRA LAYOUT**
- Xác minh Layout.liquid có `{% resources type: "Stylesheet" %}`

### **BƯỚC 2: THAY THẾ WIDGET**
- Xóa RawHtml widget hiện tại
- Tạo widget mới với STYLEBLOCK

### **BƯỚC 3: TEST**
- Refresh page và kiểm tra CSS có apply không

---

## ⚠️ **CHỜ LỆNH CỦA ANH ĐỂ THỰC HIỆN!**

**Em đã phân tích xong nguyên nhân và có 3 phương án. Anh muốn thực hiện phương án nào?**