# FHP Colony Guide Wiki — Developer Guide

Oct 31, 2026 · @Kriley Evyn

## Tổng quan

FHP Colony Guide Wiki là wiki Fandom tiếng Việt về vũ trụ giả tưởng FHP Colony (do F Colony Guider™ làm). Toàn bộ giao diện chạy bằng **một file CSS và wikitext thuần**, không có JavaScript, vì Common.js trên wiki này chỉ staff Fandom sửa được.

Repo [FHPCG-source](https://github.com/DCmee-eemCD/FHPCG-source) là kho lưu bản gốc của mọi thứ dev có thể sửa: CSS, font, template và nội dung các trang. Wiki thật không tự đồng bộ với repo; dev sửa trong repo hoặc trên wiki rồi **chép tay** sang nơi còn lại.

Tài liệu này dành cho dev muốn sửa giao diện hoặc thêm trang. Đọc xong sẽ biết file nào làm gì, class nào dùng ở đâu, và những bẫy của Fandom cần tránh.

## Cấu trúc repo

Common.css chuẩn nằm trong `Important/`, font nằm trong `font/`, template trong `template/`, còn nội dung các trang trong `page/`. File trang là wikitext thuần (`.mediawiki`), một số file không có đuôi.

| Đường dẫn | Vai trò |
| --- | --- |
| `Important/common.css` | Bản chuẩn của `MediaWiki:Common.css` (khoảng 1470 dòng, 22 mục). Chép nguyên file này lên wiki. |
| `font/SJ Jujutsu Kaisen.woff2` | Font tiêu đề. Wiki nạp qua jsDelivr theo đường dẫn `FHPCG-source@main/font/...`, nên đổi tên hoặc di chuyển file sẽ làm mất font. |
| `template/Character2 (infobox).mediawiki` | Infobox nhân vật (`{{Character2}}`). |
| `template/DocsList.mediawiki` | Bảng tài liệu chia theo cấp (Đặc Cấp đến Cấp 4), mỗi cấp là một bảng gập được. |
| `template/TheDocsWithNoGrp.mediawiki` | Danh sách tài liệu không chia cấp, tối đa 100 mục (`t1` đến `t100`). |
| `template/Discord.mediawiki` | Dòng mời vào Discord ở cuối trang (`{{Discord}}`). |
| `page/Main_Page.mediawiki` | Trang chủ đang chạy. |
| `page/NewGenMainpage.mediawiki` | Bản trang chủ cũ (legacy), không còn dùng; đừng sửa hay copy từ file này. |
| `page/Canon/` | Characters, Jujutsu, Lores, Story Arcs. |
| `page/Sorcerer/` | Chú Thuật Sư (CSR) và Chú Nguyền Sư (CUS): trang lobby và tài liệu từng nhân vật. |
| `page/FHPC Things/` | Incidents (ICD), Barriers (BRE), Chú Cụ (CST). Hiện mỗi mục chỉ có trang lobby. |
| `page/The How To Page/` | Trang hướng dẫn: lobby (`TRA_lobby`) và `TRA-001` đến `TRA-003`. |
| `page/Explanation/` | `Wikis_rule` (nội quy) và `Wikis_language` (thuật ngữ, quy tắc đánh mã tài liệu). |
| `README.md` | Ghi chú ngắn về repo và giấy phép CC-BY-SA 3.0. |

## Giới hạn của Fandom

Những điều dưới đây đã gây lỗi thật trên wiki này. Đọc trước khi viết CSS hay wikitext mới.

| Giới hạn | Hệ quả | Cách xử lý trong repo |
| --- | --- | --- |
| Không sửa được `Common.js` (chỉ staff) | Không có JavaScript: không nút Copy thật, không đếm ngược, không đổi nội dung theo giờ | Chỉ dùng CSS, wikitext, template. Ô code cho phép bấm một lần để chọn hết bằng `user-select: all`. |
| Fandom tự chèn `<p>`, `<br>` và thẻ bọc quanh các khối cách nhau bằng dòng trống | Lưới `grid`/`flex` bị lệch cột, khối bị đẩy ra | Ẩn phần tử lạ: `.fhp-cat-grid > :not(.fhp-char)` và `.fhp-logrow > br`. Trong khối code, không để dòng trống giữa các thẻ `div`. |
| `display: grid` / `flex` hay bị Fandom đè | Layout không áp dụng | Viết `!important` cho `display` và `grid-template-columns` của `.fhp-row`, `.fhp-logrow`. |
| Mục 2 ép mọi `h1`–`h6` trong bài thành gần trắng | Tiêu đề trắng trên nền trắng trong `.fhp-block` | Ghi đè `.mw-parser-output .fhp-block hN { color: #000 !important }`. |
| Link bài viết có màu hồng + glow `!important` (mục 3) | Link trong thẻ, nút trắng bị đổi màu | Selector phải bắt đầu bằng `.mw-parser-output` cùng `!important`, ví dụ `.mw-parser-output .fhp-char-name a`. |
| Fandom cắt dấu cách sau dấu phẩy trong `content` của CSS | Chữ bị dính liền (ví dụ ghi chú ở right rail) | Dùng dấu cách cứng `\0000a0` sau dấu phẩy. |
| Gallery Fandom tự dựng khung và đẩy ảnh (nhất là GIF) | Ảnh lệch, bị cắt bóng, `widths=` trong wikitext không có tác dụng | CSS ép cỡ khung bằng `!important` (biến `--nav-*` ở mục 18.4). Đổi cỡ nút phải sửa CSS, không sửa wikitext. |
| Trình soạn CSS của Fandom báo lỗi `:has()` | Thấy dấu lỗi đỏ nhưng trình duyệt vẫn chạy đúng | Bỏ qua cảnh báo; `:has()` dùng ở trang Characters. |
| Nút gập mặc định là `<button class="mw-collapsible-toggle">` có `::before` `[` và `::after` `]` | Hiện dấu ngoặc lạ khi đổi `font-size` | Mục 14 biến nút thành mũi tên SVG; ở code box phải tắt cả hai pseudo-element. |
| Sau khi lưu CSS, trình duyệt vẫn giữ bản cũ | Thấy giao diện không đổi | Nhấn `Ctrl+F5`. |

## Design tokens

Màu, font và không gian đều khai báo thành biến CSS ở mục 0 của `common.css`. Viết màu bằng `var(--...)` thay vì gõ mã hex để đổi theme chỉ cần sửa một chỗ.

| Biến | Giá trị | Dùng cho |
| --- | --- | --- |
| `--bg-main` | `#171613` | Nền đen ấm của wiki, nền ảnh nhân vật |
| `--bg-panel` | `#201F1B` | Infobox, bảng, khung code |
| `--text-main` | `#E7E6DD` | Chữ thường |
| `--text-strong` | `#FAFAF7` | Tiêu đề, nhãn đậm |
| `--text-dark` | `#171613` | Chữ trên nền sáng (header infobox, header bảng) |
| `--border` | `#8C8A7E` | Viền bảng, infobox, khung code, đường kẻ |
| `--accent` | `#ED3761` | Màu nhấn (link, mũi tên, icon, tiêu đề ghi chú) |
| `--accent-hover` | `#FF5075` | Link khi trỏ chuột, từ khóa trong code |
| `--neon-red`, `--neon-red-glow` | `#E20000`, `#FF2E2E` | Chữ spoiler |
| `--paper` | `#fffffa` | Nền khối trắng |
| `--paper-border` | `13px solid #ffffff` | Viền khối trắng |
| `--paper-shadow` | `inset 0 0 10px 2px rgba(0,0,0,0.2)` | Bóng trong khối trắng |
| `--radius` | `3px` | Bo góc mọi nơi |
| `--ease-pop` | `cubic-bezier(0.2, 0.8, 0.2, 1)` | Hiệu ứng nảy khi trỏ chuột |
| `--font-jjk` | `'Jujutsu Kaisen', sans-serif` | Tiêu đề, tên nút, nhãn |
| `--font-body` | `'Inter', sans-serif` | Chữ nội dung (nạp từ Google Fonts, độ đậm 400–700) |

Riêng hai template, `DocsList` và `TheDocsWithNoGrp` không dùng biến mà viết cứng màu bằng `style="..."` (nâu `#524a37`, be `#b5a98b`, nâu đậm `#302a26`, xám ấm `#a09581`). Muốn đổi giao diện bảng tài liệu thì sửa cả hai template, không chỉ `common.css`.

## Cấu trúc common.css

File chia thành 22 mục, mục lục nằm ở đầu file. Thêm rule mới thì đặt đúng mục; nếu là thành phần mới thì tạo mục mới trước mục Responsive.

| Mục | Nội dung | Ghi chú |
| --- | --- | --- |
| 0 | Biến CSS (`:root`) | Màu, viền khối trắng, font |
| 1 | Font, cỡ chữ tiêu đề | `@font-face` Jujutsu Kaisen từ jsDelivr; `@import` Inter ở đầu file |
| 2 | Nền bài viết và màu chữ | `.page__main` nền mờ + blur; ép `h1`–`h6` gần trắng |
| 3 | Link | Màu hồng + glow `!important` cho bài, infobox, bảng, footer |
| 4 | Portable infobox | Dùng cho `{{Character2}}` |
| 5 | Spoiler | Chữ đỏ neon (`.spoiler-text`) |
| 6 | Tabber, wikitable |  |
| 7 | Scrollbar | Tùy chỉnh kiểu WebKit |
| 8 | Nút Edit / Save / Menu | Bỏ ô trắng, chỉ giữ chữ và icon |
| 9 | Ẩn categories cuối trang |  |
| 10 | Gallery | 10.0 dùng chung, 10.1 lưới 3 cột, 10.2 nút Create Page. Không trang nào trong repo dùng `fhp-gallery-3x4` hay `fhp-create-btn`. |
| 11 | Header, logo | Phần tử `fandom-community-header` |
| 12 | Trang chủ | Ẩn tiêu đề `#firstHeading` của Main Page |
| 13 | Bo góc 3px | Selector `*` trong right rail |
| 14 | Nút gập / mở | Mũi tên SVG; ô code ghi đè ở mục 21 |
| 15 | Nút tên tài liệu (`fhp-entry-btn`) | Template đang dùng hơn 100 lần, đừng đổi tên class |
| 16 | `<hr>` |  |
| 17 | Khối trắng dùng chung | `.fhp-block`, `.fhp-cat`, `.fhp-bigbtn a` |
| 18 | Main page | 18.1 khối, 18.2 hero, 18.3 tiêu đề, 18.4 gallery Navigation, 18.5 hàng, 18.6 thẻ Canon, 18.7 nút lớn, 18.8 bảng Update, 18.9 icon mạng xã hội |
| 19 | Trang Characters | `.fhp-cat`, `.fhp-cat-grid`, `.fhp-char` |
| 20 | Right rail | Ghi chú LƯU Ý cố định bằng `::before`; độ rộng 388px |
| 21 | Code box | Gập 10 dòng, Show all, tô màu cú pháp |
| 22 | Responsive | `@media (max-width: 700px)`, luôn đặt cuối file |

Quy ước khi viết CSS:

- Class tự tạo đều bắt đầu bằng `fhp-` (ví dụ `fhp-block`, `fhp-card`, `fhp-char`).
- Selector nằm trong nội dung bài nên bắt đầu bằng `.mw-parser-output` để thắng rule của Fandom.
- Màu, font, bo góc dùng biến ở mục 0; không viết cứng mã hex mới nếu đã có biến tương đương.
- Phần co giãn cho mobile chỉ viết trong `@media` ở mục 22.
- Mỗi mục mở đầu bằng khối comment có vạch ===== và dòng `N. TÊN MỤC` và được liệt kê trong mục lục; đổi số mục thì cập nhật cả hai.
- Những giá trị hay phải chỉnh (cỡ nút Navigation, độ đen nền) đã tách thành biến hoặc có comment ngay cạnh giá trị.

## Component và wikitext mẫu

Mẫu dưới đây lấy từ các trang đang chạy trong `page/`. Khi tạo trang mới, copy cấu trúc từ trang thật thay vì viết lại, vì Fandom rất nhạy với dòng trống và thứ tự thẻ.

### Trang chủ (`Main_Page`)

Trang gồm, theo thứ tự: khối hero, hàng `fhp-row` (Navigation + Lưu ý + Official links), khối Canon, nút lớn, hàng `fhp-logrow` (hai bảng Update). Trên Main\_Page, khối `fhp-note` nằm trong thêm một `div.inter-content`; giữ nguyên cấu trúc đó khi sửa.

```wikitext
<div class="fhp-block">
<div class="fhp-block-bg">[[File:Fhpc_pic 2.png.png|link=|alt=]]</div>
<div class="fhp-block-content">
<div class="fhp-hero-title">Welcome To FHP Colony Guide!</div>
<div class="fhp-hero-version">Wiki version: Rel-0.1.1 • Discord Server: update 20</div>
<div class="fhp-hero-tagline">...</div>
<div class="fhp-hero-stats">We got {{NUMBEROFEDITS}} edits, {{NUMBEROFARTICLES}} articles, and {{NUMBEROFFILES}} files</div>
<div class="fhp-hero-note">...</div>
</div>
</div>
```

Navigation dùng gallery của Fandom. Mỗi dòng là `ảnh|'''Tên nút'''|link=Trang`. Cỡ nút do CSS quyết định (biến `--nav-img`, `--nav-gap-x`, `--nav-gap-y`, `--nav-font` ở mục 18.4), `widths=` trong wikitext bị bỏ qua. Hiện bố cục 2 nút một hàng, hợp với 6 nút; thêm nút thì phải xem lại chiều cao.

```wikitext
<div class="fhp-block fhp-block-nav">
<div class="fhp-nav-title">Navigation</div>
<div class="fhp-custom-gallery">
<gallery position="center" captionalign="center" navigation="true" captionposition="below" orientation="none" widths="300">
MrL.png|'''Chú Thuật Sư'''|link=Chú Thuật Sư
Amen.gif|'''How to'''|link=The How To Page
</gallery>
</div>
</div>
```

Thẻ Canon: ảnh nền, tên trang là link phủ cả thẻ (có `a::after`), mô tả tối đa 1/3 chiều rộng. Thẻ chẵn/lẻ tự canh trái/phải xen kẽ, không cần thêm class.

```wikitext
<div class="fhp-block fhp-block-canon">
<div class="fhp-nav-title">Canon</div>
<div class="fhp-card">
<div class="fhp-card-bg">[[File:cCard-1.png|link=Canon Characters|alt=Canon Characters]]</div>
<div class="fhp-card-title">[[Canon Characters|Characters]]</div>
<div class="fhp-card-desc">Mô tả ngắn...</div>
</div>
</div>
```

Hai bảng Update cạnh nhau và nút lớn cuối trang:

```wikitext
<div class="fhp-bigbtn">[[Wiki's Language|Don't understand? Read Wiki's Language.]]</div>

<div class="fhp-logrow">
<div class="fhp-block fhp-log">
<div class="fhp-log-title">Wiki Update</div>
== '''Rel 0.1.1''' ==
24/10/2026
</div>
<div class="fhp-block fhp-log">
<div class="fhp-log-title">Server Update</div>
</div>
</div>
```

### Trang Characters và các trang lobby có bảng phân loại

Mỗi bảng là `fhp-cat` (nhãn ở giữa cạnh trên), bên trong là `fhp-cat-grid` (tối đa 4 nút một hàng, 2 nút trên mobile, hàng thiếu nút tự canh giữa). Ảnh nhân vật có tỉ lệ 41:45. Bảng không có nút nào tự hiện dòng "Chưa có tài liệu".

```wikitext
<div class="fhp-cat">
<div class="fhp-cat-label">Chú Nguyền Sư</div>
<div class="fhp-cat-grid">

<div class="fhp-char">
<div class="fhp-char-img">[[File:Fuhkunahw.png|400px|link=Fuhkunah|alt=]]</div>
<div class="fhp-char-name">[[Fuhkunah|Fuhkunah]]</div>
</div>

</div>
</div>
```

### Danh sách tài liệu và infobox

Mỗi tài liệu trong danh sách là một `div.fhp-entry-btn` chứa link. Dùng `{{DocsList}}` khi chia cấp (Đặc Cấp, Cấp 1, Tiền Cấp 1, Cấp 2, Tiền Cấp 2, Cấp 3, Cấp 4), `{{TheDocsWithNoGrp}}` khi không chia.

```wikitext
{{DocsList
|dac-cap = 
<div class="fhp-entry-btn">[[CSR-001|CSR-001 ⋅ Văn Võ song toàn và LƯƠNG thiện]]</div>
|cap-1 = 
}}

{{TheDocsWithNoGrp
| t1 = <div class="fhp-entry-btn">[[TRA-001|TRA-001 ⋅ How to bypass firewall]]</div>
}}
```

Trang nhân vật mở đầu bằng `{{Character2}}`, các tham số gồm `title1`, `image1`, `caption-image1`, `header1`, `Name`, `Kanji`, `Romanji`, `Gender`, `Technique`, `Grade`, `Domain`, `Status`. Tham số bỏ trống thì ô tương ứng ẩn đi. Cuối trang nên có `{{Discord}}`.

```wikitext
{{Character2
| title1 = Tên hiển thị
| image1 = Ten file.png
| Name = ...
| Gender = Nam
| Technique = Thuật thức 1 • Thuật thức 2
| Grade = Đặc Cấp
| Status = Còn hoạt động
}}
```

### Ô code có nút Show all

Khung gập mặc định ở khoảng 10 dòng, bấm vào code là chọn hết (không có nút Copy vì cần JS). Đừng chừa dòng trống giữa các thẻ `div` bên ngoài, Fandom sẽ chèn `<p>` và làm vỡ khung. Thay `lang` theo ngôn ngữ để được màu cú pháp. Muốn hiện sẵn toàn bộ thì bỏ `mw-collapsed`.

```wikitext
<div class="mw-collapsible mw-collapsed fhp-codebox" data-expandtext="Show all" data-collapsetext="Show less">
<div class="fhp-code-hint">Click vào khung code để chọn hết, rồi Ctrl+C</div>
<div class="mw-collapsible-content"><syntaxhighlight lang="js">
...code...
</syntaxhighlight></div>
</div>
```

### Icon mạng xã hội

Icon vẽ bằng CSS mask, không cần thư viện Bootstrap Icons. Hiện có ba icon: `bi-facebook`, `bi-youtube`, `bi-discord`. Thêm icon mới: copy SVG, thêm một rule `.mw-parser-output .bi-<tên>` dùng `mask-image` như mục 18.9.

```wikitext
* <i class="bi bi-discord"></i>'''[https://discord.gg/5AeqceAJu2 Discord server]'''
```

## Quy trình làm việc

Repo chỉ là nơi lưu bản gốc. Không có bot hay GitHub Actions đồng bộ lên wiki, nên mọi thay đổi phải được chép thủ công vào wiki. Repo chưa có quy định về pull request hay review; hãy thống nhất với maintainer qua Discord trước khi sửa file lớn.

**Sửa giao diện (CSS)**

1. Sửa `Important/common.css` trong repo, đặt rule đúng mục (xem phần Cấu trúc common.css).
2. Kiểm tra ngoặc `{}` khớp nhau. Bản hiện tại đang cân bằng; một dấu sai sẽ làm cả phần CSS phía sau không chạy.
3. Dán toàn bộ file vào `MediaWiki:Common.css` trên wiki (cần quyền admin), lưu lại.
4. Mở wiki, nhấn `Ctrl+F5`. Xem cả ở cỡ màn hình rộng và ở chiều rộng dưới 700px (mobile).
5. Nếu lỗi, bấm F12, dùng công cụ chọn phần tử để xem Fandom đã chèn thẻ gì; đừng đoán cấu trúc HTML.
6. Khi ổn, commit bản đã dán lên wiki về repo để repo luôn khớp wiki.

**Sửa trang chủ**

- `NewGenMainpage` là bản cũ (legacy, không còn dùng), `Main_Page` là bản đang chạy: sửa ở đây trên wiki rồi lưu bản đó về repo.
- Chỉnh `Rel` và `update` trong `fhp-hero-version`, sau đó viết bảng Wiki Update và Server Update ở cuối trang.
- Giữ nguyên thứ tự và cấu trúc các thẻ `div`.

**Thêm tài liệu mới**

1. Đặt tên trang là mã tài liệu (ví dụ `CSR-002`). Mã trong bảng dưới đây.
2. Trang nhân vật mở đầu bằng `{{Character2}}`, kết thúc bằng `{{Discord}}`. Bọc phần thân trong `<div class="inter-content">` để dùng font Inter.
3. Thêm nút vào trang lobby tương ứng: `{{DocsList}}` (theo cấp) hoặc `{{TheDocsWithNoGrp}}`. Mỗi mục bọc trong `fhp-entry-btn`.
4. Thêm bản sao vào `page/` trong repo, đặt đúng thư mục.
5. Nhân vật canon còn phải thêm `fhp-char` vào đúng bảng `fhp-cat` của trang `Canon Characters`.

| Loại | Mã | Lobby |
| --- | --- | --- |
| Chú Thuật Sư | `CSR-xxx` | `CSR_lobby` |
| Chú Nguyền Sư | `CUS-xxx` | `CUS_lobby` |
| Nguyền Hồn | `CPR-xxx` |  |
| Thức Thần | `SKM-xxx` |  |
| Incident | `ICD-xxx` | `ICD_lobby` |
| Barrier | `BRE-xxx` | `BRE_lobby` |
| Chú Cụ | `CST-xxx` | `CST_lobby` |
| Chú Vật | `CSO-xxx` |  |
| Tutorial | `TRA-xxx` | `TRA_lobby` |

Mã lấy từ trang `Wikis_language`, chỗ duy nhất khác là mục Sorcerer ở đó ghi `CRS-xxx`, còn trang thực tế dùng `CSR-001`; xem phần lỗi bên dưới.

## Lỗi thường gặp và việc còn tồn

Bảng dưới liệt kê các lỗi đã xảy ra thật khi dựng giao diện. Trước khi sửa, kiểm tra đã lưu CSS và nhấn `Ctrl+F5` chưa.

| Triệu chứng | Nguyên nhân | Cách sửa |
| --- | --- | --- |
| `fhp-row` hoặc `fhp-logrow` không chia cột | Fandom đè `display` | `display: grid !important` và `grid-template-columns` có `!important` (mục 18.5, 18.8) |
| Nút/ô trong lưới lệch tâm, một nút đơn bị lệch | Fandom chèn `<p>`, `<br>` vào giữa | Ẩn phần tử không phải `.fhp-char` (mục 19); bỏ dòng trống thừa trong wikitext |
| Tiêu đề `==` trắng trên nền trắng | Mục 2 ép tiêu đề gần trắng | `.mw-parser-output .fhp-block hN` màu đen `!important` (mục 18.8) |
| Thẻ Canon không bấm được, hoặc hiện nguyên wikitext | Thiếu lớp phủ `a::after`, hoặc `[[File:...]]` không có `link=` | Giữ rule `.fhp-card-title a::after`; viết `[[File:x.png\|link=Trang\|alt=]]` (`alt=` chỉ là chữ thay thế ảnh, có thể để trống) |
| GIF trong Navigation bị tụt xuống, ảnh không đổi cỡ | Fandom đặt `top` và kích thước riêng cho gallery | Khung ép cỡ bằng `!important`, ảnh `position: static` (mục 18.4) |
| Nút Show all hiện dấu `[` | `::before` mặc định của Fandom | `.fhp-codebox > .mw-collapsible-toggle::before { content: none !important }` (mục 21) |
| Nút Edit/Save có ô trắng, chữ mờ | Nền trắng do Fandom đặt, chữ và icon đã bị mục 8 ép thành trắng | `background: transparent !important` cho `.page-header__actions .wds-button` (mục 8) |
| Icon `<i class="bi ...">` không hiện | Wiki không nạp Bootstrap Icons, class không có rule | Thêm rule mask như mục 18.9 |
| Ghi chú right rail dính chữ sau dấu phẩy | Fandom cắt dấu cách trong `content` | `\0000a0` sau dấu phẩy (mục 20) |
| Right rail thu gọn rồi vẫn còn khoảng trống | Rule độ rộng 388px áp cả khi đang ẩn | Giữ `:not(.is-rail-hidden)` (mục 20) |
| Khối Navigation đệm trống phía dưới, hoặc cao hơn cột bên phải | Khối kéo cao bằng tổng khối Lưu ý + Official links | Chỉnh `--nav-img`, `--nav-gap-y`, `--nav-font` ở mục 18.4, mỗi lần đổi 10px rồi xem lại |

### Tồn đọng đã biết

- **Chưa tối ưu hiệu năng.** Chưa xử lý: `@import` font Inter chặn render, `backdrop-filter: blur` trên `.page__main`, `will-change` ở thẻ Canon, selector `*` ở right rail, ảnh nặng.
- **Navigation trên mobile chưa kiểm tra.** Ảnh 190px cộng khoảng cách ngang 40px có thể chỉ vừa 1 nút một hàng; muốn 2 nút thì thêm biến nhỏ hơn trong `@media`.
- **Mục CSS có thể bỏ.** Không trang nào trong repo dùng mục 10.1 (`fhp-gallery-3x4`) và 10.2 (`fhp-create-btn`).
- **Lỗi nội dung đã sửa trong repo, cần dán lên wiki.** Mã Sorcerer trong `Wikis_language` đổi thành `CSR-xxx`; dấu `'''` không khớp ở Main\_Page, `Story Arcs/lobby`, `Lores/lobby` và một dòng `'''` thừa ở trang Fuhkunah. Repo đã đúng, wiki có thể chưa.
- **Chưa có nút Copy thật, đếm ngược reset, hiện nội dung theo giờ.** Cần JS; phải xin staff Fandom duyệt qua Special:Contact.
- **README của repo** chỉ có vài dòng, nên trỏ tới tài liệu này.
