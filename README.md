# Phân tích bố cục & tư duy Flexbox — Website Trường Đại học Thủy lợi

> **Trang khảo sát:** [https://tlu.edu.vn/](https://tlu.edu.vn/) — Trang chủ
> **Thời điểm:** 25/11/2026
> **Phạm vi:** 13 khối nội dung trong `<main>`, header sticky, drawer mobile, thanh nút nổi, footer.
> **Cách khảo sát:** đọc trực tiếp cây DOM + computed style trên trình duyệt (viewport 526×558). Bố cục desktop được suy ra từ các class responsive có trong markup (`sm:`, `md:`, `lg:`, `min-[850px]:`, `min-[1025px]:`).
> **Công nghệ nhận diện được:** Tailwind CSS (utility-first), Swiper (slider ngang/dọc), sticky header, accordion bằng `<details>/<summary>` ở mobile, ảnh `object-cover` + `aspect-ratio`, màu thương hiệu xanh `#0118d8`.

---

## 1. Bản đồ tổng thể (tư duy div thuần)

Trang được tổ chức theo **một trục dọc duy nhất**, chia thành các khối lớn xếp chồng. Hai lớp "nổi" (drawer, nút liên hệ) nằm ngoài luồng bằng `position: fixed`. Toàn bộ cây dưới đây chỉ dùng `div` để thể hiện tư duy bố cục; phần tử nội dung thật sẽ được ánh xạ sang HTML ngữ nghĩa ở mục 3.

```text
div.page
├─ div.site-header                         // sticky top-0, z-30
│  ├─ div.header-bar                       // logo + tiện ích
│  └─ div.main-nav                         // thanh menu xanh (desktop ≥1025px)
│
├─ div.mobile-drawer                       // off-canvas trái, fixed, 340px
│  ├─ div.drawer-head
│  ├─ div.drawer-search
│  ├─ div.drawer-nav
│  └─ div.drawer-foot
│
├─ div.overlay                             // nền mờ chặn tương tác khi mở drawer
│
├─ div.main
│  ├─ div.hero                (1)  // slider ảnh/video toàn chiều ngang
│  ├─ div.section-news        (2)  // Tin tức (2/3) + Thông báo (1/3)
│  ├─ div.section-events      (3)  // Sự kiện — slider thẻ
│  ├─ div.section-stats       (4)  // Những con số ấn tượng
│  ├─ div.banner              (5)  // ảnh banner full-width
│  ├─ div.section-admissions  (6)  // Tuyển sinh — 2 cột ảnh/chữ
│  ├─ div.section-training    (7)  // Đào tạo — 3 cột thẻ
│  ├─ div.section-units       (8)  // Các đơn vị đào tạo, KHCN — slider 2 nửa
│  ├─ div.section-grid3       (9)  // Hợp tác quốc tế — 3 thẻ + danh sách
│  ├─ div.section-grid3       (10) // Nghiên cứu — 3 thẻ + danh sách
│  ├─ div.section-partners    (11) // Mạng lưới đối tác — dải logo
│  ├─ div.section-grid3       (12) // Vì cộng đồng — 3 thẻ + danh sách
│  └─ div.section-gallery     (13) // Khoảnh khắc TLU — lưới ảnh
│
├─ div.float-actions                       // cột nút liên hệ bên phải, fixed
└─ div.site-footer
   ├─ div.footer-top
   ├─ div.footer-cols
   └─ div.footer-bottom
```

**Quy ước đặt tên dùng trong tài liệu (BEM-lite):**

| Khối | Vai trò |
|---|---|
| `div.container` | Khung giới hạn bề ngang, lặp ở mọi section: `mx-auto max-w-[1400px] px-4 sm:px-6 lg:px-8` |
| `div.section-head` | Cụm tiêu đề mục (chữ căn giữa + gạch chân đỏ ngắn) |
| `div.section` | Một dải nội dung dọc, nền trắng hoặc xanh `#0118d8` |
| `div.card` | Thẻ nội dung tái sử dụng (media + body) |
| `div.card-media` / `div.card-body` | Nửa ảnh / nửa chữ của thẻ |
| `div.slider` / `div.slider-track` | Khung cắt + dải trượt ngang (Swiper) |
| `div.stat-item`, `div.notice-item` | Mục lặp trong danh sách |

**Nhịp dọc & khung ngang (quan sát từ markup):**

- Container: `max-w-[1400px]` (riêng stats/footer dùng `1440px`), padding `px-4 → sm:px-6 → lg:px-8`.
- Khoảng thở section: `py-10 sm:py-12 / 14 / 16`.
- Header cao `76px` (mobile) → `88px` (sm+); thanh nav cao `40px` (`h-10`).

---

## 2. Phân tích từng thành phần

### 2.1 Header (sticky) + tiện ích

```text
div.site-header
├─ div.header-bar
│  └─ div.container.header-inner        // flex, space-between, cao 76/88px
│     ├─ div.brand                      // w-[220px] h-[48px], không co
│     │  └─ div.brand-logo              // ảnh logo
│     └─ div.header-actions             // ml-auto, flex, gap 8–16px
│        ├─ div.search-box              // ẩn dưới 1025px
│        │  ├─ div.search-field         // ô input 500px, bo tròn
│        │  └─ div.search-btn           // nút tròn absolute right-3
│        ├─ div.lang-switch             // công tắc VN/EN
│        │  ├─ div.lang-input
│        │  └─ div.lang-track
│        └─ div.menu-toggle             // nút hamburger
└─ div.main-nav
   └─ div.container.nav-inner           // flex, cao 40px
      └─ div.nav-list                   // flex, items-stretch, w-full
         └─ div.nav-item × 8            // flex-auto, vách border-right
            ├─ div.nav-link
            └─ div.nav-dropdown         // absolute top-full, w-72
               └─ div.dropdown-list
                  └─ div.dropdown-item × n
```

**Tư duy Flexbox:**

- **Hai đầu trục ngang:** `header-inner` dùng `display:flex; justify-content:space-between; align-items:center` → đẩy logo sang trái, cụm tiện ích sang phải. Ngoài ra cụm phải còn dùng `margin-left:auto` — cách "đẩy nhóm sang phải" không cần đổi `justify-content`.
- **Phần tử bất biến:** logo đặt `w-[220px] h-[48px]` + `flex: none` → không bị co khi bề ngang thiếu, giữ đúng tỉ lệ thương hiệu.
- **Khoảng cách bằng `gap`:** `gap-2 sm:gap-4` thay cho margin giữa các nút tiện ích.
- **Search "icon nằm trong ô":** ô input `position: relative`, nút search `absolute right-3 top-1/2 -translate-y-1/2` + `flex items-center justify-center` → căn giữa nút theo trục dọc của input.
- **Nav chia đều:** `ul.nav-list` là `flex`, `h-full` để cao bằng thanh xanh; mỗi `li` `flex: 1 1 auto` phủ kín bề ngang; `align-items: stretch` cho link cao hết ô; `a` bên trong `flex items-center justify-center gap-1.5` → chữ + mũi tên căn giữa cả 2 trục.
- **Dropdown không dùng flex:** menu con là khối `absolute top-full` (định vị, không phải flex), mặc định `invisible`, hiện khi hover mục cha (`group-hover`). Kỹ thuật "absolute + hover" tránh đẩy layout.
- **Sticky:** cả `header` là `sticky top-0 z-30` + đổ bóng nhẹ; nav nằm bên trong header nên dính theo.
- **Mobile:** search và nav desktop bị ẩn (`hidden min-[1025px]:block`), chỉ còn logo + công tắc ngôn ngữ + hamburger → thu gọn bằng responsive, không cần flex-wrap.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Thương hiệu | `<a href="/"><img src alt="Trường Đại học Thủy lợi"></a>` | `alt` mô tả logo |
| Tìm kiếm | `<form role="search">` + `<label class="sr-only">` + `<input type="search">` + `<button type="submit" aria-label="Tìm kiếm">` | Nút chứa `<svg aria-hidden="true">` |
| Chuyển ngôn ngữ | `<label>` + `<input type="checkbox" class="sr-only">` + `<span class="track">` + cờ `<img>` | Công tắc là checkbox ẩn, không phải div |
| Hamburger | `<button aria-expanded aria-controls="mobile-nav" aria-label="Mở menu">` | Trạng thái mở/đóng qua JS |
| Menu chính | `<nav aria-label="Điều hướng chính"><ul><li><a>` | Mục có con: `<li><a aria-haspopup="true" aria-expanded>` + `<ul>` lồng |

---

### 2.2 Drawer mobile (menu trượt)

```text
div.mobile-drawer                        // aside fixed inset-y-0 left-0, w-340px max-w-[90vw]
├─ div.drawer-head                       // flex, min-h-16, space-between
│  ├─ div.drawer-title
│  └─ div.drawer-close
├─ div.drawer-search
│  └─ div.search-box
├─ div.drawer-nav                        // flex-1 + overflow-y-auto
│  └─ div.drawer-group × 8               // details/summary
│     ├─ div.drawer-summary              // flex, space-between
│     └─ div.drawer-submenu
│        └─ div.drawer-link × n
└─ div.drawer-foot
```

**Tư duy Flexbox:**

- **Cột toàn chiều cao:** `display:flex; flex-direction:column` trên drawer; header và footer giữ chiều cao tự nhiên, phần giữa `flex: 1` + `overflow-y:auto` + `overscroll-contain` → vùng menu tự cuộn, không cuộn cả trang. Đây là mẫu "full-height column with internal scroll" rất phổ biến.
- **Thanh tiêu đề:** `flex items-center justify-between gap-3` → chữ trái, nút đóng phải.
- **Accordion không cần JS:** mỗi nhóm dùng `<details>/<summary>`; `summary` là `flex items-center justify-between` để mũi tên luôn nằm cuối dòng. Nội dung con chỉ việc đổ xuống dưới khi mở.
- **Lớp phủ:** `div.overlay` là `fixed inset-0` nền tối — không tham gia flex, chỉ chặn click.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Ngăn kéo | `<aside id="mobile-nav">` | Kèm `aria-hidden`/`inert` khi đóng |
| Tiêu đề + đóng | `<div>` + `<button aria-label="Đóng menu">` | |
| Nhóm menu | `<details><summary>…</summary><ul><li><a></ul></details>` | `summary` thay cho div bấm |
| Chân drawer | `<div>` + `<p><small>` | Thông tin phụ/ghi chú |

---

### 2.3 Hero — slider toàn chiều ngang

```text
div.hero                                 // section, overflow-hidden
├─ div.hero-track                        // swiper-wrapper: flex row
│  └─ div.hero-slide × 7                 // swiper-slide: width 100%, flex-shrink 0
│     └─ div.hero-media                  // picture / video, object-cover
├─ div.hero-dots                         // absolute bottom, flex
│  └─ div.hero-dot × 7
└─ div.hero-status                       // vùng thông báo cho screen reader
```

**Tư duy Flexbox / layout:**

- **"Một hàng ngang vô hạn":** track là `flex` với các slide `width:100%; flex-shrink:0`; slider dịch chuyển bằng `transform: translateX()` chứ **không** dùng `justify-content` hay `overflow` từng slide. Đây là bản chất của mọi carousel full-width.
- **Không flex nội dung:** mỗi slide chỉ chứa ảnh/video phủ kín (`object-cover` + tỷ lệ khung), nội dung không cần căn chỉnh flex — tránh lồng flex không cần thiết.
- **7 slide:** 5 ảnh (`<picture>` — có thể có `<source>` cho màn hình lớn/nhỏ) + 2 video nền.
- **Chấm điều hướng:** dãy `span` bo tròn nằm `absolute` đáy, kích thước cố định, chấm active đổi màu — không ảnh hưởng layout ảnh.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Banner đầu trang | `<section aria-label="Banner">` + `<div class="swiper">` | `<h1 class="sr-only">` nếu trang thiếu H1 |
| Ảnh responsive | `<picture><source media="…"><img alt="…" loading="eager"></picture>` | Slide đầu ưu tiên tải |
| Video nền | `<video autoplay muted loop playsinline poster="…">` | `muted` bắt buộc để autoplay |
| Điều hướng | `<button aria-label="Chuyển tới slide 1">` | Thêm `aria-current` cho slide đang xem |

---

### 2.4 Tin tức + Thông báo (khối 2/3 – 1/3)

```text
div.section-news
└─ div.container
   └─ div.news-grid                      // grid: 1 cột → lg: 3 cột (2 + 1), gap-40px
      ├─ div.news-main                   // lg: col-span-2
      │  ├─ div.section-head             // "TIN TỨC"
      │  └─ div.news-split               // grid 2fr 1fr, gap 30px (ẩn ở mobile)
      │     ├─ div.news-feature          // 2fr
      │     │  └─ div.card.news-card--lg
      │     │     ├─ div.card-media      // aspect-[5/4]
      │     │     └─ div.card-body       // p-6
      │     │        ├─ div.card-cat     // "TIN TỨC CHUNG"
      │     │        ├─ div.card-title   // h3 + mũi tên
      │     │        ├─ div.card-excerpt
      │     │        └─ div.card-date
      │     └─ div.news-sub              // 1fr, slider dọc 2 bài phụ
      │        └─ div.card.news-card--row × 2
      │           ├─ div.card-thumb      // aspect-video, shrink-0
      │           └─ div.card-body       // flex-1 min-w-0 flex-col justify-center
      └─ div.news-side                   // 1 cột
         ├─ div.section-head             // "THÔNG BÁO"
         └─ div.notice-list              // divide-y
            └─ div.notice-item × 6       // px-4 py-4
               ├─ div.notice-title       // line-clamp-2
               └─ div.notice-date
```

**Tư duy Flexbox / Grid:**

- **Cấp trang dùng Grid, không Flex:** `lg:grid-cols-3` + `col-span-2` cho cột tin chính → tỉ lệ 2:1 ổn định, không cần tính `flex-basis`.
- **Trong cột tin chính lại Grid:** `grid-cols-[2fr_1fr]` tạo "bài chủ đạo 2 phần + 2 bài phụ 1 phần" — tỷ lệ 2:1 rõ ràng; `gap:30px` tách rời hai vùng.
- **Thẻ phụ là hàng ngang:** thumbnail `aspect-[16/9]` + `flex-shrink:0`, phần chữ `flex:1; min-width:0; display:flex; flex-direction:column; justify-content:center` → ảnh giữ khung, chữ co giãn và căn giữa theo chiều cao ảnh; `min-w-0` chống tràn khi tiêu đề dài.
- **Danh sách thông báo dùng block + `divide-y`:** nội dung ngắn, chỉ 1 chiều → không cần flex, kẻ dòng bằng `border-top` giữa các `li`.
- **Mobile tách nhánh:** bản desktop `max-md:hidden`, bản mobile `md:hidden` → hai cấu trúc khác nhau, tránh nhồi một flex phức tạp cho mọi kích thước.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Dải nội dung | `<section>` + `<div class="container">` | |
| Tiêu đề mục | `<h2><a href="/tin-tuc">TIN TỨC</a></h2>` | Site đang dùng `<a>` trần — nên bọc H2 |
| Bài chủ đạo | `<article><a href><img alt></a><div><span class="cat">…</span><h3><a>…</a></h3><p>…</p><time datetime="2026-10-19">19/10/2026</time></div></article>` | `time` cần `datetime` |
| Bài phụ | `<article>` + `<a><img></a>` + `<h3><a>` + `<time>` | |
| Thông báo | `<ul><li><a><span>…</span><time>13/11/2026</time></a></li></ul>` | Danh sách tin ngắn |

---

### 2.5 Sự kiện — slider thẻ

```text
div.section-events
└─ div.container
   ├─ div.section-head
   ├─ div.slider                         // overflow-hidden
   │  └─ div.slider-track                // flex, transition-transform
   │     └─ div.event-card × 8           // article, h-full
   │        ├─ div.event-media           // aspect-video
   │        │  ├─ div.event-img
   │        │  └─ div.event-date         // absolute left-3 top-3, flex-col
   │        │     ├─ div.date-day
   │        │     └─ div.date-month
   │        └─ div.event-body            // p-4
   │           └─ div.event-title        // line-clamp-2
   └─ div.slider-dots                    // flex justify-center gap-2
      └─ div.dot × 8                     // active dài hơn (w-7)
```

**Tư duy Flexbox:**

- **Dải trượt ngang:** track `display:flex`; mỗi thẻ `flex: 0 0 auto` với bề ngang theo breakpoint, `h-full` để mọi thẻ **cao bằng nhau** (nhờ `align-items: stretch` mặc định) dù tiêu đề dài ngắn khác nhau.
- **Huy hiệu ngày:** `absolute left-3 top-3` + `display:flex; flex-direction:column; align-items:center` → hai dòng "19" và "THÁNG 11" xếp dọc và căn giữa theo chiều ngang của huy hiệu.
- **Chấm điều hướng:** `flex justify-center gap-2`; chấm active tăng `width` (2.5 → 7) với transition → hiệu ứng "thanh dài ra" thay vì đổi màu đơn thuần.
- **Cắt tràn:** slider cha `overflow:hidden`, track dịch bằng transform — không sinh scrollbar.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Thẻ sự kiện | `<article>` | |
| Ngày | `<time datetime="2026-11-19" class="badge"><span>19</span><span>THÁNG 11</span></time>` | |
| Ảnh | `<a href><img alt="…" loading="lazy"></a>` | |
| Tiêu đề | `<h3><a>…</a></h3>` | `line-clamp-2` |
| Điều hướng | `<button aria-label="Xem sự kiện tiếp theo">` | |

---

### 2.6 Những con số ấn tượng (stats)

```text
div.section-stats                        // id="stats"
└─ div.container
   ├─ div.section-head                   // h2
   └─ div.stats-grid                     // grid 2 cột → min-[850px]: 5 cột
      └─ div.stat-item × 6               // flex-col items-center
         ├─ div.stat-value               // dd, order-1, text-3xl, tabular-nums
         └─ div.stat-label               // dt, order-2, max-w-190px
```

**Tư duy Flexbox:**

- **Cột dọc căn giữa:** mỗi mục `display:flex; flex-direction:column; align-items:center; text-align:center` → số và nhãn xếp dọc, cùng canh giữa trục ngang.
- **Đảo thứ tự thị giác bằng `order`:** trong DOM là `<dt>` (nhãn) rồi `<dd>` (số), nhưng CSS đặt `dt { order:2 }`, `dd { order:1 }` → **số hiện trước, nhãn hiện sau** mà vẫn giữ đúng cặp `dt/dd` ngữ nghĩa. Đây là ví dụ tinh tế nhất về Flexbox `order` trên trang.
- **Grid cho lưới số:** mobile 2 cột, mục đầu `col-span-2` (nhấn mạnh "Người học"), desktop 5 cột; `gap-y-9` canh hàng dọc.
- **Số liệu động:** giá trị khởi tạo `0+` rồi đếm tăng; `tabular-nums` giữ các chữ số cùng bề rộng để số không "nhảy" khi chạy.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Cụm số liệu | `<dl>` + `<div><dt>Người học</dt><dd>24.000+</dd></div>` | `div` bọc cặp là hợp lệ trong `<dl>` |
| Số đếm | `<dd><span data-count="24000">24.000+</span></dd>` | Thêm text tĩnh dự phòng nếu JS lỗi |
| Nhãn | `<dt>` | |

---

### 2.7 Banner full-width

```text
div.banner                               // aspect-[1020/392], overflow-hidden
└─ div.banner-media                      // img object-cover
```

- Không dùng Flexbox: chỉ một ảnh tỷ lệ cố định phủ kín bề ngang (`aspect-ratio` thay cho padding-hack cũ).
- Ảnh là **một đơn vị thị giác** ngăn cách hai dải nội dung trắng.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Banner | `<a href="…"><picture><source><img alt="…"></picture></a>` | Nếu chỉ trang trí: `alt=""` + `aria-hidden` |
| Tiêu đề ẩn (nếu cần) | `<h2 class="sr-only">` | Giữ cấu trúc heading |

---

### 2.8 Tuyển sinh — bố cục 2 cột ảnh/chữ

```text
div.section-admissions
└─ div.container
   └─ div.admission-grid                 // grid 1 → 2 cột, items-center, gap-8
      ├─ div.admission-media             // aspect-[4/3], overflow-hidden
      │  └─ div.media-img
      └─ div.admission-body
         ├─ div.section-head             // căn giữa mobile → trái desktop
         ├─ div.admission-desc           // p
         ├─ div.admission-list
         │  └─ div.admission-item × 3    // flex items-center gap-3
         │     ├─ div.item-icon          // flex h-4 w-4 rounded-full
         │     └─ div.item-text
         └─ div.admission-cta            // text-center md:text-left
            └─ div.btn                   // inline-flex px-6 py-3
```

**Tư duy Flexbox:**

- **Căn giữa theo chiều dọc cột:** `items-center` trên Grid đặt ảnh và khối chữ thẳng hàng giữa theo trục dọc.
- **Hàng "icon + chữ":** `display:flex; align-items:center; gap:12px` — icon là `span` tròn 16px căn giữa bên trong (`flex items-center justify-center`), chữ nằm cạnh; `gap` thay margin.
- **Nút CTA:** `display:inline-flex` → nút ôm nội dung nhưng vẫn căn giữa nhãn theo trục dọc; không cần `line-height` thủ công.
- **Chuyển căn lề theo breakpoint:** `text-center md:text-left` — mobile căn giữa cho dễ đọc, desktop căn trái để thẳng hàng với ảnh.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Khối giới thiệu | `<section>` | |
| Ảnh | `<a href="/tuyen-sinh"><img alt="…" loading="lazy"></a>` | |
| Tiêu đề | `<h2><a>TUYỂN SINH</a></h2>` | Site đang dùng `<a>` trần |
| Mô tả | `<p>` | |
| Danh sách lợi ích | `<ul><li><span aria-hidden="true">›</span>…</li></ul>` | Icon trang trí ẩn với AT |
| Nút hành động | `<a class="btn" href="…">ĐĂNG KÝ NGAY</a>` | |

---

### 2.9 Đào tạo — 3 cột thẻ

```text
div.section-training
└─ div.container
   ├─ div.section-head                   // "ĐÀO TẠO"
   └─ div.training-grid                  // grid 1 → 3 cột
      └─ div.training-col × 3
         ├─ div.section-head--sm         // tiêu đề nhỏ
         └─ div.card
            ├─ div.card-media            // aspect-video
            └─ div.card-body             // p-4
```

- Lưới thuần 1 chiều lặp lại → **Grid** là lựa chọn đúng; Flexbox chỉ xuất hiện bên trong thẻ (nếu cần hàng meta).
- Ba cột cùng cấu trúc nên tái sử dụng `div.card` — tư duy "component hóa" thay vì viết lặp.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Khối | `<section>` | |
| Thẻ | `<article><a><img></a><div><span class="cat">…</span><h3><a>…</a></h3></div></article>` | |
| Tiêu đề mục | `<h2>` hoặc `h2 > a` | |

---

### 2.10 Các đơn vị đào tạo, KHCN — slider 2 nửa

```text
div.section-units
└─ div.unit-slider                        // flex-col (mobile) → 2 nửa ngang
   ├─ div.unit-left                       // flex-col justify-center items-start
   │  ├─ div.unit-title                   // h2
   │  ├─ div.unit-names                   // slider dọc
   │  │  └─ div.unit-name × n
   │  ├─ div.fade-top                     // gradient che mép trên
   │  └─ div.fade-bottom
   └─ div.unit-right                      // flex items-center justify-center
      ├─ div.unit-bg                      // ảnh nền
      └─ div.unit-content                 // card kính mờ
         ├─ div.unit-subtitle
         ├─ div.unit-desc
         └─ div.unit-cta
```

**Tư duy Flexbox:**

- **Khung 2 nửa:** mobile xếp dọc (`flex-direction: column`), desktop hai nửa ngang; nửa trái là **cột** `justify-content:center; align-items:flex-start` → dồn tiêu đề + danh sách vào giữa theo chiều cao, canh trái.
- **Slider dọc:** track là `flex-direction: column`, di chuyển translateY — cùng nguyên lý slider ngang nhưng đổi trục.
- **"Stage" bên phải:** `display:flex; align-items:center; justify-content:center` → nội dung overlay (khối kính mờ) luôn nằm giữa ảnh nền bất kể kích thước.
- **Mặt nạ gradient:** hai dải `fade-top`/`fade-bottom` phủ mép để danh sách dọc "tan dần" — chi tiết thị giác hay, không ảnh hưởng flex.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Khối | `<section>` | |
| Tiêu đề | `<h2>` | |
| Danh sách đơn vị | `<div role="tablist"><button role="tab" aria-selected>` | Danh sách tên đổi nội dung bên phải |
| Nội dung đơn vị | `<div role="tabpanel"><h3>…</h3><p>…</p><a>…</a></div>` | |
| Ảnh nền | `<img alt="" aria-hidden="true">` hoặc CSS background | |

---

### 2.11 Các khối "3 thẻ + danh sách" (Hợp tác quốc tế · Nghiên cứu · Vì cộng đồng)

Ba section dùng chung một khuôn:

```text
div.section-grid3
└─ div.container
   ├─ div.section-head
   └─ div.card-grid                       // grid 3 cột card + 1 cột list (min 230px)
      ├─ div.card × 3                    // article, hover nâng nhẹ
      │  ├─ div.card-media               // aspect-video, bg xám
      │  └─ div.card-body                // p-4
      │     ├─ div.card-cat
      │     ├─ div.card-title
      │     ├─ div.card-excerpt
      │     └─ div.card-date
      └─ div.mini-list                   // divide-y, nền xám nhạt
         └─ div.mini-item × 3            // article py-4
            ├─ div.mini-title
            └─ div.mini-date
   └─ div.card-slider-mobile             // md:hidden — 3 thẻ thành carousel
```

**Tư duy Flexbox / Grid:**

- **Lưới bất đối xứng:** `grid-cols-[repeat(3,minmax(0,1fr))_minmax(230px,1fr)]` → 3 cột thẻ chia đều + cột danh sách rộng tối thiểu 230px. `minmax(0,1fr)` chống tràn nội dung dài (điều mà `1fr` thuần có thể gây ra).
- **`min-w-0`:** từng cột đặt `min-width:0` để chữ/ảnh không đẩy vỡ lưới.
- **Danh sách phụ dùng block:** `divide-y` + `article` — không cần flex cho nội dung 1 chiều.
- **Mobile đổi thành slider:** ẩn lưới, hiện carousel → giữ trải nghiệm vuốt thay vì xếp 3 thẻ dọc dài.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Khối | `<section>` | |
| Thẻ tin | `<article>` + `<a>` + `<h3>` + `<p>` + `<time>` | |
| Danh sách phụ | `<ul><li><a>…</a><time>…</time></li></ul>` | |
| Tiêu đề mục | `<h2>` (site dùng `<a>` — nên sửa) | |

---

### 2.12 Mạng lưới đối tác — dải logo

```text
div.section-partners                    // nền xanh #0118d8, text trắng
└─ div.container                        // text-center
   ├─ div.section-head                  // h2
   ├─ div.section-desc                  // max-w-3xl mx-auto
   └─ div.partner-strip                 // nền trắng, px-8/12
      └─ div.partner-track              // swiper flex
         └─ div.partner-logo × n        // flex h-[90px] items-center justify-center
```

**Tư duy Flexbox:**

- **Căn giữa cả khối:** section `text-align:center`, đoạn mô tả `margin-inline:auto` + `max-width` → dòng chữ không quá dài.
- **Ô logo đồng đều:** mỗi logo là `display:flex; height:90px; align-items:center; justify-content:center` → mọi logo cùng khung cao, tự canh giữa dù tỉ lệ ảnh khác nhau (rất quan trọng với logo đối tác).
- **Slider vô hạn:** thanh logo chạy lặp (Swiper virtual slides) — cùng mẫu track flex ngang.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Khối | `<section>` | |
| Tiêu đề/mô tả | `<h2>`, `<p>` | |
| Danh sách logo | `<ul aria-label="Đối tác"><li><img alt="Tên đối tác" loading="lazy"></li></ul>` | Alt phải là tên đơn vị |

---

### 2.13 Khoảnh khắc TLU — lưới ảnh

```text
div.section-gallery
└─ div.container
   ├─ div.section-head
   └─ div.gallery-grid                  // grid-cols-3 auto-rows-[100px], nhiều ô span
      └─ div.gallery-item × 15          // button, overflow-hidden, rounded-lg
         └─ div.gallery-img             // object-cover, hover scale-105
```

- **Grid 2 chiều thật sự:** các ô có `row-span`/`col-span` khác nhau tạo bố cục mosaic — Flexbox không làm được việc này (flex chỉ xếp 1 chiều, muốn mosaic phải lồng nhiều cột thủ công).
- Hiệu ứng hover: `overflow:hidden` + `transform: scale(1.05)` trên ảnh.
- Mobile: lưới thu về 3 cột với chiều cao ô nhỏ hơn (auto-rows co giãn).

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Lưới ảnh | `<ul><li><button type="button" aria-label="Xem ảnh …"><img alt=""></button></li></ul>` | Ảnh trang trí `alt=""` |
| Lightbox (nếu có) | `<dialog>` + `<button aria-label="Đóng">` | Hiện đại, không cần lib |

---

### 2.14 Thanh nút liên hệ nổi

```text
div.float-actions                        // nav fixed right-4 top-[24vh], z-20
└─ div.float-btn × 4                     // flex-col gap-2
   └─ div.icon                           // h-10 w-10 flex items-center justify-center rounded-l-lg
```

**Tư duy Flexbox:**

- `display:flex; flex-direction:column; gap:8px` → cột nút dọc, khoảng cách đều.
- Mỗi nút `h-10 w-10` + `flex items-center justify-center` → icon luôn là tâm ô, kể cả khi kích thước icon khác nhau.
- Ẩn dưới `md` (`hidden md:flex`) — tránh che nội dung trên di động.

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Liên hệ nhanh | `<nav aria-label="Liên hệ nhanh"><a href="tel:…" aria-label="Gọi điện"></a>…</nav>` | `rel="noopener"` cho link ngoài |
| Icon | `<svg aria-hidden="true" focusable="false">` | |

---

### 2.15 Footer

```text
div.site-footer                          // nền xanh, text trắng
└─ div.container
   ├─ div.footer-top                     // grid: 2 cột → [1.3fr 1fr 1fr 1fr]
   │  ├─ div.footer-brand                // logo 280×105
   │  └─ div.footer-info × 3             // flex items-start gap-2
   │     ├─ div.info-icon                // span shrink-0
   │     └─ div.info-text                // min-w-0
   │        ├─ div.info-label
   │        └─ div.info-value            // break-words
   ├─ div.footer-cols                    // grid 1 → 2 → 4 cột
   │  └─ div.footer-col × 4
   │     ├─ div.col-title                // h3 + gạch chân 3px
   │     ├─ div.col-list                 // ul space-y-4
   │     │  └─ div.col-item × n
   │     └─ div.col-map                  // chỉ cột 4: nút map 152px
   └─ div.footer-bottom
      └─ div.copyright                   // p text-center
```

**Tư duy Flexbox:**

- **Cột thương hiệu rộng hơn:** Grid `lg:grid-cols-[1.3fr_1fr_1fr_1fr]` → logo + địa chỉ chiếm thêm không gian.
- **Hàng "icon + 2 dòng chữ":** `display:flex; align-items:flex-start; gap:8px`; icon `flex-shrink:0` (không bị bóp méo), khối chữ `min-width:0` + `break-words` → email/địa chỉ dài tự xuống dòng thay vì tràn. Đây là cặp `shrink-0` + `min-w-0` kinh điển.
- **Danh sách link:** `space-y-4` (margin-top giữa các `li`) thay `gap` vì danh sách là block — đơn giản hơn flex-column cho nội dung thuần chữ.
- **Nút bản đồ toàn ô:** `flex h-full w-full items-center justify-center` → nút lấp đầy ô lưới, chữ căn giữa; chiều cao đồng bộ cột (`h-[152px]`).
- **Hai tầng Grid:** tầng 1 (thương hiệu + liên hệ) và tầng 2 (4 cột link) tách biệt → dễ responsive độc lập (2 cột → 4 cột).

| Kiểu nội dung | Phần tử HTML | Ghi chú |
|---|---|---|
| Chân trang | `<footer>` | |
| Địa chỉ | `<address>` + `<p>` | |
| Email/điện thoại | `<a href="mailto:daihocthuyloi@tlu.edu.vn">`, `<a href="tel:+842438522201">` | Không chỉ text trơn |
| Cột link | `<section><h3>GIỚI THIỆU</h3><ul><li><a></a></li></ul></section>` | Gạch chân là `<span>` trang trí |
| Bản đồ | `<a href="…">Tải bản đồ tương tác</a>` hoặc `<iframe title="Bản đồ trường">` | Iframe cần `title` + `loading="lazy"` |
| Bản quyền | `<p><small>© 2026 …</small></p>` + `<a>` | |

---

## 3. Tổng hợp

### 3.1 Bảng ánh xạ nội dung → phần tử HTML

| Nhóm nội dung | Phần tử HTML | Xuất hiện ở |
|---|---|---|
| Khung trang | `<header> <nav> <main> <section> <article> <aside> <footer>` | Toàn trang |
| Thương hiệu | `<a><img alt></a>` | Header, footer |
| Điều hướng | `<nav><ul><li><a>`, `<details><summary>` (mobile) | Header, drawer, footer, nút nổi |
| Tìm kiếm | `<form role="search">`, `<input type="search">`, `<button>` | Header, drawer |
| Công tắc ngôn ngữ | `<label>` + `<input type="checkbox">` + `<span>` | Header |
| Banner/slider | `<section>`, `<picture>/<video>`, `<button aria-label>` | Hero, sự kiện, đối tác, đơn vị |
| Tiêu đề mục | `<h2>` (bọc link nếu cần) | 13 section |
| Bài viết | `<article><a><img></a><h3><a></h3><p><time datetime>` | Tin tức, đào tạo, 3 khối grid |
| Danh sách tin ngắn | `<ul><li><a><span><time>` | Thông báo, mini-list |
| Số liệu | `<dl><div><dt><dd>` | Stats |
| Lưới ảnh | `<ul><li><button><img></button>` / `<dialog>` | Gallery |
| Liên hệ | `<address>`, `<a href="mailto:">`, `<a href="tel:">` | Footer |
| Icon | `<svg aria-hidden="true">` | Khắp nơi |
| Bản quyền | `<p><small>` | Footer |

### 3.2 Các mẫu Flexbox lặp lại trên trang

| # | Mẫu | Nơi dùng | Cách viết |
|---|---|---|---|
| 1 | Hai cụm hai đầu | Header | `justify-content: space-between` + `margin-left:auto` |
| 2 | Chia đều 1 chiều | Nav, slider track | `flex: 1 1 auto` / `flex: 0 0 auto` |
| 3 | Icon + chữ căn giữa | Nút, li, stats | `display:flex; align-items:center; gap` |
| 4 | Đảo thứ tự thị giác | Stats | `order` trên `dt/dd` |
| 5 | Cột cao bằng khung + cuộn | Drawer | `flex-direction:column` + `flex:1` + `overflow-y:auto` |
| 6 | Không co / không tràn | Logo, footer info, thẻ phụ | `flex:none` / `flex-shrink:0` + `min-width:0` |
| 7 | Cột dọc căn giữa | Unit-left, stat-item, unit-right | `flex-direction:column; justify-content:center; align-items:center` |
| 8 | Ô cố định chứa ảnh | Partner logo, icon nút | `height cố định` + `flex; align-items:center; justify-content:center` |
| 9 | Grid khi cần 2 chiều | Tin tức, card, gallery, footer | `grid-template-columns`, `minmax`, `span`, `auto-rows` |
| 10 | Mobile-first responsive | Toàn trang | 1 cột → 2/3/4/5 cột; ẩn/hiện nav; slider thay lưới |

### 3.3 Skeleton HTML ngữ nghĩa (từ cây div)

```html
<body>
  <header class="site-header">
    <div class="container header-inner">
      <a class="brand" href="/"><img src="logo.svg" alt="Trường Đại học Thủy lợi"></a>
      <div class="header-actions">
        <form role="search" class="search-box">
          <label class="sr-only" for="q">Tìm kiếm</label>
          <input id="q" type="search" placeholder="Tìm kiếm...">
          <button type="submit" aria-label="Tìm kiếm"><svg aria-hidden="true"></svg></button>
        </form>
        <label class="lang-switch">
          <input type="checkbox" class="sr-only">
          <span class="track">VN</span>
        </label>
        <button type="button" aria-label="Mở menu" aria-controls="mobile-nav" aria-expanded="false">
          <svg aria-hidden="true"></svg>
        </button>
      </div>
    </div>
    <nav class="main-nav" aria-label="Điều hướng chính">
      <div class="container">
        <ul class="nav-list">
          <li><a href="/gioi-thieu">GIỚI THIỆU</a>
            <ul class="dropdown"><li><a href="#">Cơ cấu tổ chức</a></li></ul>
          </li>
          <!-- ... 7 mục còn lại ... -->
        </ul>
      </div>
    </nav>
  </header>

  <aside id="mobile-nav" class="mobile-drawer">
    <div class="drawer-head"><strong>MENU</strong><button aria-label="Đóng menu">×</button></div>
    <nav class="drawer-nav" aria-label="Điều hướng di động">
      <details><summary>GIỚI THIỆU</summary><ul><li><a href="#">Tổng quan</a></li></ul></details>
      <!-- ... -->
    </nav>
  </aside>

  <main>
    <section class="hero" aria-label="Banner">
      <div class="slider-track">
        <div class="hero-slide"><picture><source media="(min-width:768px)" srcset="hero-lg.jpg"><img src="hero-sm.jpg" alt="…"></picture></div>
        <div class="hero-slide"><video autoplay muted loop playsinline poster="p.jpg" src="hero.mp4"></video></div>
      </div>
      <div class="hero-dots"><button aria-label="Slide 1" aria-current="true"></button></div>
    </section>

    <section class="section-news">
      <div class="container news-grid">
        <div class="news-main">
          <h2 class="section-head"><a href="/tin-tuc">TIN TỨC</a></h2>
          <div class="news-split">
            <article class="card card--lg">
              <a href="#"><img src="a.jpg" alt="…"></a>
              <div class="card-body">
                <span class="cat">Tin tức chung</span>
                <h3><a href="#">Thông báo Tuyển dụng giảng viên…</a></h3>
                <p>Căn cứ nhu cầu giảng dạy…</p>
                <time datetime="2026-10-19">19/10/2026</time>
              </div>
            </article>
            <div class="news-sub">
              <article class="card card--row">
                <a href="#"><img src="b.jpg" alt="…"></a>
                <div class="card-body"><span class="cat">Tin tức chung</span><h3><a href="#">…</a></h3><time datetime="2026-11-25">25/11/2026</time></div>
              </article>
            </div>
          </div>
        </div>
        <aside class="news-side">
          <h2 class="section-head"><a href="/thong-bao">THÔNG BÁO</a></h2>
          <ul class="notice-list">
            <li><a href="#"><span>Sinh viên Thủy lợi hưởng ứng…</span><time datetime="2026-11-13">13/11/2026</time></a></li>
          </ul>
        </aside>
      </div>
    </section>

    <section class="section-events">
      <div class="container">
        <h2 class="section-head"><a href="/su-kien">SỰ KIỆN</a></h2>
        <div class="slider">
          <div class="slider-track">
            <article class="event-card">
              <a href="#"><img src="e.jpg" alt="…"><time datetime="2026-11-19" class="badge"><span>19</span><span>THÁNG 11</span></time></a>
              <h3><a href="#">Trực tiếp Lễ kỷ niệm…</a></h3>
            </article>
          </div>
        </div>
        <div class="slider-dots"><button aria-label="Trang 1" aria-current="true"></button></div>
      </div>
    </section>

    <section id="stats" class="section-stats">
      <div class="container">
        <h2 class="section-head">NHỮNG CON SỐ ẤN TƯỢNG</h2>
        <dl class="stats-grid">
          <div class="stat-item"><dt>Người học</dt><dd><span data-count="24000">24.000+</span></dd></div>
          <div class="stat-item"><dt>Đối tác trong &amp; ngoài nước</dt><dd><span data-count="500">500+</span></dd></div>
          <div class="stat-item"><dt>Sinh viên tốt nghiệp có việc làm</dt><dd><span data-count="95">95%</span></dd></div>
          <div class="stat-item"><dt>Ngành học</dt><dd><span data-count="75">75+</span></dd></div>
          <div class="stat-item"><dt>Giảng viên Tiến sĩ</dt><dd><span data-count="60">60%</span></dd></div>
        </dl>
      </div>
    </section>

    <section class="banner"><a href="#"><picture><img src="banner.jpg" alt="Khơi nguồn – Trí tuệ – Tỏa sáng"></picture></a></section>

    <section class="section-admissions">
      <div class="container admission-grid">
        <a class="admission-media" href="/tuyen-sinh"><img src="ts.jpg" alt="Sinh viên TLU" loading="lazy"></a>
        <div class="admission-body">
          <h2 class="section-head"><a href="/tuyen-sinh">TUYỂN SINH</a></h2>
          <p>Cung cấp thông tin tuyển sinh, ngành tuyển sinh, đề án tuyển sinh…</p>
          <ul class="admission-list">
            <li><span aria-hidden="true">›</span> Thông tin tuyển sinh</li>
          </ul>
          <div class="admission-cta"><a class="btn" href="/tuyen-sinh">ĐĂNG KÝ NGAY</a></div>
        </div>
      </div>
    </section>

    <section class="section-training">
      <div class="container">
        <h2 class="section-head"><a href="/dao-tao">ĐÀO TẠO</a></h2>
        <div class="training-grid">
          <article class="card"><a href="#"><img src="dt1.jpg" alt="…" loading="lazy"></a><div class="card-body"><span class="cat">Đào tạo</span><h3><a href="#">Thông tin luận án…</a></h3></div></article>
        </div>
      </div>
    </section>

    <section class="section-units">
      <div class="unit-slider">
        <div class="unit-left">
          <h2>CÁC ĐƠN VỊ ĐÀO TẠO, KHCN</h2>
          <div class="unit-names" role="tablist" aria-label="Chọn đơn vị">
            <button role="tab" aria-selected="true">KHOA CÔNG NGHỆ THÔNG TIN</button>
            <button role="tab" aria-selected="false">KHOA CƠ KHÍ</button>
          </div>
        </div>
        <div class="unit-right" role="tabpanel">
          <h3>KHOA KẾ TOÁN VÀ KINH DOANH</h3>
          <p>Khoa Kế toán và Kinh doanh…</p>
          <a href="#">Xem thêm</a>
        </div>
      </div>
    </section>

    <section class="section-grid3">
      <div class="container">
        <h2 class="section-head"><a href="/hop-tac">HỢP TÁC QUỐC TẾ</a></h2>
        <div class="card-grid">
          <article class="card"><a href="#"><img src="ht1.jpg" alt="…"></a><div class="card-body"><span class="cat">Hợp tác quốc tế</span><h3><a href="#">…</a></h3><p>…</p><time datetime="2026-11-20">20/11/2026</time></div></article>
          <aside class="mini-list"><article><h3><a href="#">…</a></h3><time datetime="2026-10-23">23/10/2025</time></article></aside>
        </div>
      </div>
    </section>

    <section class="section-partners">
      <div class="container">
        <h2>MẠNG LƯỚI ĐỐI TÁC</h2>
        <p>Trường Đại học Thủy lợi tự hào là đối tác của…</p>
        <ul class="partner-strip" aria-label="Đối tác">
          <li><img src="p1.png" alt="Tên đối tác" loading="lazy"></li>
        </ul>
      </div>
    </section>

    <section class="section-gallery">
      <div class="container">
        <h2>KHOẢNH KHẮC TLU</h2>
        <ul class="gallery-grid">
          <li><button type="button" aria-label="Xem ảnh 1"><img src="g1.jpg" alt="" loading="lazy"></button></li>
        </ul>
      </div>
    </section>
  </main>

  <nav class="float-actions" aria-label="Liên hệ nhanh">
    <a href="tel:…" aria-label="Gọi điện"><svg aria-hidden="true"></svg></a>
    <a href="https://zalo.me/…" aria-label="Zalo" rel="noopener"><svg aria-hidden="true"></svg></a>
  </nav>

  <footer class="site-footer">
    <div class="container">
      <div class="footer-top">
        <div class="footer-brand"><img src="logo-white.svg" alt="Trường Đại học Thủy lợi"></div>
        <address class="footer-info"><span class="info-icon"><svg aria-hidden="true"></svg></span><p>175 Tây Sơn – P. Kim Liên – Hà Nội</p></address>
        <div class="footer-info"><span class="info-icon"><svg aria-hidden="true"></svg></span><p><a href="mailto:daihocthuyloi@tlu.edu.vn">daihocthuyloi@tlu.edu.vn</a></p></div>
        <div class="footer-info"><span class="info-icon"><svg aria-hidden="true"></svg></span><p><a href="tel:+842438522201">(024) 38522201</a></p></div>
      </div>
      <div class="footer-cols">
        <section><h3>GIỚI THIỆU</h3><ul><li><a href="#">Tổng quan</a></li></ul></section>
        <section><h3>TRUY CẬP NHANH</h3><ul><li><a href="#">eTLU</a></li></ul></section>
        <section><h3>THEO DÕI TLU</h3><ul><li><a href="#" rel="noopener">Facebook</a></li></ul></section>
        <section><h3>TRƯỜNG ĐẠI HỌC THỦY LỢI</h3><a class="map-cta" href="#">Tải bản đồ tương tác</a></section>
      </div>
      <p class="copyright"><small>© 2026 Trung tâm Công nghệ số &amp; Học liệu TLU</small></p>
    </div>
  </footer>
</body>
```

### 3.4 Breakpoint & lưới quan sát được

| Breakpoint | Giá trị | Thay đổi chính |
|---|---|---|
| Mặc định | < 640px | 1 cột; nav + search ẩn; drawer + hamburger; stats 2 cột |
| `sm` | ≥ 640px | Header cao 88px; footer 2 cột; padding tăng |
| `md` | ≥ 768px | Thẻ tin thành slider; nút nổi hiện; một số khối 2 cột |
| `min-[850px]` | ≥ 850px | Stats chuyển 5 cột |
| `min-[1025px]` | ≥ 1025px | Nav desktop + search hiện (`hidden` → `block`) |
| `lg` | ≥ 1024px | News 3 cột (2+1); footer 4 cột; lưới thẻ 3 cột |

**Chuyển đổi Flexbox ↔ Grid được dùng có chủ đích:**

- **Flexbox** cho: thanh công cụ (header), menu nav, hàng icon–chữ, cột nội dung có vùng cuộn (drawer), slider (1 hàng), badge nhiều dòng.
- **Grid** cho: chia cột theo tỉ lệ (tin tức 2:1, footer 1.3:1:1:1), lưới thẻ, mosaic ảnh, dải stats.
- Nguyên tắc rút ra: *một chiều → Flexbox; hai chiều hoặc tỉ lệ cột → Grid.*

### 3.5 Nhận xét & đề xuất cải thiện ngữ nghĩa

1. **Tiêu đề mục đang là `<a>` trần** (TIN TỨC, SỰ KIỆN, TUYỂN SINH, ĐÀO TẠO, HỢP TÁC QUỐC TẾ, NGHIÊN CỨU, VÌ CỘNG ĐỒNG…). Nên bọc `<h2><a>…</a></h2>` để đúng cấu trúc heading, tốt cho SEO và screen reader.
2. **`<time>` thiếu `datetime`** ở hầu hết thẻ tin; nên thêm `datetime="2026-11-25"` để máy đọc được.
3. **Slider chưa có nhãn ARIA đầy đủ:** thêm `aria-roledescription="carousel"`, `aria-label`, và `aria-current` trên dot đang chọn.
4. **Số liệu đếm khởi tạo 0:** cần text tĩnh dự phòng trong markup để không hiển thị "0+" khi JS chậm/lỗi.
5. **Drawer off-canvas:** cần `inert`/`aria-hidden` khi đóng và bẫy focus khi mở để đảm bảo a11y.
6. **Hai cấu trúc nav trùng lặp** (desktop + drawer) là chấp nhận được, nhưng nên đảm bảo chỉ một bản được tiếp cận bởi bàn phím tại mỗi thời điểm.
7. **Điểm mạnh:** dùng `details/summary` cho accordion mobile (không cần JS), `aspect-ratio` thay padding-hack, `min-w-0` + `shrink-0` xử lý tràn chữ tốt, `order` cho stats rất "flexbox-thinking", tách Grid/Flex đúng chỗ.

---

## 4. Đẩy lên GitHub

File này sẵn sàng làm `README.md` của repository. Các lệnh gợi ý:

```bash
git init
git add README.md
git commit -m "docs: phân tích bố cục & tư duy Flexbox trang chủ tlu.edu.vn"
git branch -M main
git remote add origin https://github.com/<username>/<repo>.git
git push -u origin main
```

> Nếu repo đã tồn tại: bỏ `git init`/`remote add`, chỉ cần `git add README.md && git commit && git push`.

---

*Tài liệu phân tích phục vụ học tập bố cục web. Cấu trúc phản ánh trạng thái trang chủ tại thời điểm khảo sát 25/11/2026 và có thể thay đổi khi website cập nhật.*
