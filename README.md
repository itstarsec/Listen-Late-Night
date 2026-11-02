Tôi đính kèm Listen_Night_v42_Full_Source_and_Rebuild_Kit.zip. Hãy đóng vai Senior Full-Stack Engineer, Three.js Engineer và chuyên gia Cloudflare D1/Sites. Dựng lại ứng dụng Listen Night thành một Site mới độc lập, chạy thật, có backend bền vững và giữ giao diện, chức năng của mã nguồn đính kèm. Thực hiện đến khi có URL sử dụng được, không chỉ tư vấn hoặc dựng mockup.

1. Nguồn chuẩn và phạm vi

Đây là mã v42, commit ed82cca0f6055c8de85adb401048bbffd0f89537; thư mục source/ có 209 file tracked nguyên bản. Mã đính kèm là nguồn chuẩn, không tái tạo giao diện từ mô tả, ảnh, website tham khảo hay trí nhớ.

Đọc lần lượt BAT_DAU_TAI_DAY.md, RESTORE_AND_DEPLOY.md, FEATURES_AND_ACCEPTANCE.md, data/inventory.json; kiểm tra checksum bằng python3 tools/verify-bundle.py. source/README.md, source/VERIFICATION.md và một số tài liệu khác chứa lịch sử nhiều phiên bản; tình trạng v42 và snapshot trong gói này có ưu tiên cao hơn các con số/luồng cũ.

Mục tiêu là bảo toàn toàn bộ mã UI/player/API hiện tại. Không đổi framework, màu, khoảng cách, hình học cần kim, giao diện quản trị hoặc trò ném file; không nâng dependency hoặc thay lockfile chỉ vì có phiên bản mới.

Gói có 75 session: 73 playlist công khai và 2 session chỉ mở qua liên kết; tổng 2.495 vị trí bài, 2.324 video ID duy nhất. Thư viện công khai có 2.476 vị trí, gộp còn 2.305 video. Đây là snapshot ngày 01/11/2026, không phải dịch vụ đồng bộ toàn bộ YouTube.

Không được ghi, publish, xoá hay thay auth, secrets, audience, database hoặc lịch chạy của website gốc. Project ID cũ chỉ là provenance. Không tái sử dụng database cũ hoặc token cũ.

2. Chuẩn bị bản sao đúng cách

Giải nén và chạy công cụ đi kèm: node tools/prepare-new-site.mjs /workspace/sites/listen-night-rebuilt với đích mới, trống, nằm ngoài gói export. Có thể chọn tên thư mục khác. Công cụ từ chối ghi đè thư mục đã có nội dung.

Chế độ mặc định là phục hồi dữ liệu thật. Không dùng --seed-only trừ khi tôi yêu cầu bỏ snapshot đang lưu. Công cụ bỏ project_id cũ, thêm data/runtime-snapshot.json, bộ phục hồi một lần lib/restore-snapshot.ts và wrapper ở API route. Những file giao diện, logic phát nhạc, auth và schema được giữ nguyên.

Dùng skill/quy trình Sites hiện có trong phiên mới để đăng ký Site mới duy nhất. Lưu ID mới thực tế trả về vào manifest. Giữ d1: "DB", r2: null, capabilities: ["mcp"]. Không dùng nguyên manifest có ID cũ trong source/ để sửa Site gốc.

Giữ stack React 19.2.6, Vinext 1.0.0-beta.5, Vite 8.0.13, Three.js 0.186.x, TypeScript 5.9.3 và toàn bộ pnpm-lock.yaml. Node >=22.13, khuyến nghị Node 24; pnpm pin 11.25.0. Dùng installer hiện hành của Sites; không copy node_modules từ gói vì gói chỉ chứa source/lockfile.

Giữ các source build trong build/, scripts, compatibility layer ES5 và vendor/. Không lược chúng vì tưởng là file sinh tự động.

3. Backend và dữ liệu thật

D1 là nguồn lưu playlist, tên, thứ tự và quyền. localStorage chỉ lưu vị trí nghe/tùy chọn của từng thiết bị.

Áp dụng bốn migration schema gốc theo thứ tự. Không nhét snapshot hoặc backfill lớn vào migration schema.

Trên database mới, trống, lần gọi API đầu tiên phục hồi 9 bảng từ snapshot trong một D1 batch nguyên tử, ghi receipt một lần trong seeds. Công cụ từ chối database đang có dữ liệu mà chưa có receipt, rollback nếu có lỗi và hỗ trợ retry. Không xoá bảng, không reset session để đăng nhập.

Phải gọi GET /api/collections sau khi schema sẵn sàng để hoàn tất phục hồi trước khi chạy MCP/lịch bảo trì. Sau đó reload chỉ đọc receipt, không khôi phục dữ liệu cũ đè các sửa đổi của quản trị viên.

Giữ đủ 75 session, cả tên, slug, thứ tự, version, thời điểm thêm, seed markers, metadata, import receipts và dữ liệu bảo trì. Hai session không nằm trong catalogue tiếp tục không bị đưa vào thư viện công khai.

Kiểm tra bim: Nhạc Việt, đủ 25 bài theo thứ tự snapshot, đầu w8r5zDA4awM, cuối X9N4QJpK7TA, không thêm trùng.

Giữ be-ngu-ngoan: tên Baby Sleep Instantly, duy nhất 377o0R09Y0U. Giữ paris-at-night-jazz: duy nhất EQfKnvCFs1Y. Giữ cả ba playlist Nhạc AI và tất cả category hiện có.

Không tự nhập lại album/Mix từ YouTube, không suy ra mã RD từ video, không thay snapshot bằng kết quả tìm kiếm mới.

4. Giao diện và trải nghiệm phải giữ

Tiếng Việt, nền charcoal #121314, accent hổ phách #FF9D3C, typography/spacing từ CSS gốc. Desktop: tìm kiếm đầu trang, thư viện trái, console/bàn đĩa giữa, hàng phát phải thu gọn, transport cố định dưới. Mobile: tìm kiếm, thông tin bài, bàn đĩa, video, danh sách; bottom sheets, safe area và vùng chạm phù hợp.

/ desktop mặc định Tất cả bài hát, gộp video ID duy nhất, thứ tự ổn định. Mobile <=900px mở ngẫu nhiên một playlist có bài, không tải toàn bộ thư viện khi mới vào. ?view=all mở tổng hợp; ?s=<slug> luôn là session slug và có ưu tiên.

Một YouTube IFrame Player API chính thức, khởi tạo một lần và tái sử dụng. Giữ video hiển thị, playsinline, phát sau tương tác, “Chạm để phát”, trước/phát-pause/tiếp/tua, shuffle, lặp tắt/một/tất cả, ended tự chuyển và skip lỗi có giới hạn.

Duyệt playlist, tìm kiếm, mở hàng đợi, đổi giao diện, sửa tên hoặc thêm/xoá bài khác không reset player hay mất tiến độ. Hàng phát độc lập với playlist đang duyệt; xoá current chọn successor nhất quán.

Three.js thực: thân máy/chân, mâm, đĩa, nhãn, trục, cần kim liền mạch, đối trọng, cartridge, đèn và ba nút điều khiển. Đĩa/kim/đèn phản ánh trạng thái thực của YouTube, không giả “theo nhịp”. Giữ damping, giới hạn camera, reset, auto rotate mặc định tắt, phân biệt click-drag và bật tương tác 3D có chủ đích trên mobile.

Tải 3D bất đồng bộ, một canvas, tái sử dụng geometry/material, DPR giới hạn, ngừng render khi tab ẩn/scene ngoài vùng nhìn, cleanup đầy đủ, reduced motion. Giữ SVG fallback đã sửa cần kim và loại vật thể dư, cùng thao tác nghe khi WebGL lỗi/mất context.

Giữ /lite và tự chuyển iOS/Safari cũ sang ES5/XHR, cùng backend, 20 bài mỗi trang và toàn bộ hàng phát. Không buộc iOS 12.5.8 chạy bundle React/Three.js.

5. Quản trị, tài khoản và nhập dữ liệu

/bim là trang quản trị ẩn, không có nút đăng nhập trên trang nghe. /?s=bim là playlist, không phải trang quản trị.

Giữ console tập trung quản lý, không nhúng player hay bàn đĩa vào admin. Tạo/đổi tên/xoá playlist, thêm URL nhiều dòng, xem trước và báo số thêm/trùng/lỗi, metadata, tìm/lọc/phân trang, xoá bài đơn/bulk, đổi thứ tự, xuất URL và modal căn giữa hoạt động thật.

Giữ parser URL watch/youtu.be/Shorts/music/mobile/ID; chuẩn hoá URL, bỏ tracking/list/index, dedup theo ID và giữ dòng hợp lệ khi dòng khác lỗi. Kiểm tra frontend/backend, Origin, version/If-Match và idempotency. Không render HTML người dùng/oEmbed.

Giữ Listen Court: TXT/CSV/TSV/M3U/M3U8 chứa URL, đặt tên playlist trước; chọn file rồi kéo thẻ tự do từ vị trí bấm, kéo xuống lấy lực, radar chấm đỏ dự đoán cùng mô phỏng, ném có xoay, va chạm vành có thể bật ra/vào, chỉ upload sau khi lọt qua lưới; có thao tác nhập trực tiếp bằng HTML. Không đổi thành animation giả upload thành công.

Tài khoản cấu hình qua runtime: AUTH_USERNAME, AUTH_PASSWORD_HASH, AUTH_ADDITIONAL_ADMINS; username mong muốn thanhlm và tuantn, cùng quyền quản trị. Gói không chứa mật khẩu, hash thật hoặc phiên đang đăng nhập. Dùng mật khẩu tôi cung cấp trong phiên mới, tạo hash mới qua scripts/hash-password.mjs và lưu bằng secret runtime; không hardcode vào source/prompt/log.

Đặt ADMIN_ORIGIN bằng origin HTTPS thật của Site mới, không dấu / cuối. Không copy origin cũ. APP_ALLOWED_ORIGINS chỉ thêm proxy HTTPS cụ thể khi cần; không wildcard. Giữ cookie Secure/HttpOnly/SameSite và credential tags riêng từng tài khoản để tránh lỗi đăng nhập rồi logout. Không giả header danh tính để thay login ứng dụng.

6. Bảo trì và lịch chạy

Giữ MCP video_maintenance_status, run_video_maintenance({limit:20}); chỉ writer được cấp quyền, email owner đúng và danh tính Sites thật. MAINTENANCE_OWNER_EMAIL là secret của Site mới. YouTube API key là tuỳ chọn, thiếu key vẫn dùng public embed preview. Cloudflare fetch dùng redirect:'manual'.

Backend tự chọn ID đang có, ưu tiên video player báo lỗi, 360 ID/ngày, tối đa 18 lô 20. Chỉ gỡ sau hai lỗi rõ ràng cùng lý do cách nhau >=6 giờ; lưu bản sao và chặn thêm lại. Không xoá dựa trên mạng/bot/login/region, lỗi 5/153 hoặc tìm kiếm. Dừng khi daily_limit/complete/busy, hai lô toàn unknown liên tiếp, lỗi quyền hoặc lưu.

Đọc config/video-maintenance-schedule.json. Sau khi Site mới chạy và MCP kết nối hợp lệ, tạo lịch riêng của Site mới 03:30 hằng ngày Asia/Bangkok bằng Sites/automation hỗ trợ. Kiểm tra trùng trước khi tạo. Không dùng ID của lịch cũ, không sửa lịch đang gắn với Site gốc. Nếu quyền/kết nối chưa sẵn sàng, báo blocker chính xác; không dùng token bypass hoặc giả identity header.

7. Kiểm tra và bàn giao

Trên checkout mới: chạy node tests/run.mjs (baseline 163 test), TypeScript --noEmit, rồi node <export-kit>/tools/test-restore.mjs <checkout>; kiểm tra restore/retry/rollback/từ chối DB có dữ liệu, catalogue và hai tài khoản fixture. Build qua quy trình Sites hiện hành, push đúng commit, lưu version và deploy Site mới.

Website nghe được công khai, còn mọi thao tác quản trị vẫn cần cookie. Giữ Site gốc nguyên trạng. Không bịa ID, domain hay kết quả kiểm thử.

Sau publish: gọi API để phục hồi, chạy node <export-kit>/tools/verify-live.mjs <NEW_SITE_ORIGIN>; kiểm tra đủ 73 công khai +75 session, bim25, baby1, Paris1, global dedup, reload không đổi version và /lite.

Browser QA thực tế: desktop/mobile, play/pause/seek/ended, duyệt playlist không ngắt nhạc, hàng đợi, 3D/SVG và thao tác upload ném file. Phân biệt rõ mock test, preview nhúng và phát thực tế; không nhận là đã kiểm tra iPhone/iPad/Android vật lý nếu chưa làm.

Bàn giao URL thật, source Site mới, ảnh kiểm chứng và những cấu hình còn cần tôi hoàn tất. Không cam kết bỏ quảng cáo, phát nền/khóa màn hình hay mọi video đều hoạt động ở mọi vùng: các phần này do YouTube/trình duyệt quyết định. “Giống 100%” ở đây là bảo toàn source/UI/chức năng nội bộ và phục hồi đúng snapshot; không phải bảo đảm dịch vụ bên ngoài bất biến.

Hãy bắt đầu đọc gói, kiểm tra tính toàn vẹn và triển khai ngay. Tự quyết định chi tiết vận hành nhỏ theo mã có sẵn; chỉ hỏi khi thiếu quyền hoặc secret cần thiết, đồng thời tiếp tục phần độc lập làm được
