# Content Atelier — AI Content Studio

เว็บต้นแบบที่เตรียมโครงสร้างสำหรับใช้งานจริง 5 หน้า: Dashboard, AI Content Generator, Product Library, Content Results และ Content Calendar

## สิ่งที่มีในชุดนี้
- เพิ่ม/แก้ไข/ลบสินค้า พร้อมหมวดหมู่ รูปสินค้าแบบ URL กลิ่น จุดเด่น ราคา โปรโมชั่น และข้อห้าม
- สร้างคอนเทนต์ภาษาไทยผ่าน Gemini API โดยเรียกจาก Supabase Edge Function (ไม่ฝัง AI key ใน browser)
- บันทึกผลงาน เปลี่ยนสถานะ คัดลอกข้อความ และค้นหาผลงาน
- ปฏิทินคอนเทนต์และกำหนดวันโพสต์
- สมัคร/เข้าสู่ระบบด้วย Supabase Auth และแยกข้อมูลผู้ใช้ด้วย Row Level Security
- โหมดทดลองเก็บข้อมูลใน localStorage เมื่อยังไม่เชื่อม backend; ข้อมูลนี้อยู่ในเบราว์เซอร์เครื่องนั้น ไม่ซิงก์ข้ามอุปกรณ์

**หมายเหตุ:** ไฟล์ ZIP เป็น source code ที่ต้องตั้งค่าและ deploy เอง ยังไม่ใช่เว็บไซต์ออนไลน์ที่พร้อมใช้งานทันที ระบบ AI และฐานข้อมูลออนไลน์จะเริ่มทำงานหลังทำขั้นตอนด้านล่าง

## 1. สร้าง Supabase database
1. สร้างโปรเจกต์ที่ https://supabase.com/ (ตรวจสอบแผนและโควตาปัจจุบันของบริการ)
2. เปิด SQL Editor → New query แล้วคัดลอกไฟล์ `supabase.sql` ทั้งหมดไป Run
3. ไปที่ Project Settings → API (หรือหน้า Connect/API Keys ตามหน้าตา dashboard) แล้วคัดลอก Project URL และ `anon`/`publishable` key
4. เปิดเว็บไซต์ กด “ตั้งค่าการเชื่อมต่อ” ที่แถบด้านซ้าย วาง URL และ key แล้วบันทึก
5. กด “เข้าสู่ระบบ / สมัครสมาชิก” เพื่อสร้างบัญชี
6. หากเปิด email confirmation ให้กดยืนยันอีเมลก่อนล็อกอิน ตรวจ Spam/Junk ด้วย

ใช้เฉพาะ anon/publishable key ใน frontend และห้ามใส่ `service_role` key ในเว็บไซต์เด็ดขาด นโยบาย RLS ใน SQL จำกัดการเข้าถึงตามบัญชีที่เข้าสู่ระบบ

## 2. เปิดใช้งาน AI ผ่าน Gemini API
1. เข้า https://aistudio.google.com/ สร้าง Gemini API key ตามเงื่อนไข/โควตาที่หน้า Google แสดงสำหรับบัญชีของคุณ
2. ติดตั้ง Supabase CLI ตาม https://supabase.com/docs/guides/cli
3. ใน Terminal ที่โฟลเดอร์โปรเจกต์ รัน:
   ```sh
   supabase login
   supabase link --project-ref YOUR_PROJECT_REF
   supabase secrets set GEMINI_API_KEY=YOUR_GEMINI_API_KEY
   supabase functions deploy generate-content
   ```
4. กลับไปหน้าเว็บ เข้าสู่ระบบ แล้วลองสร้างคอนเทนต์

โควตา free tier, รุ่นโมเดล, ราคา, และข้อกำหนด API เปลี่ยนได้ตามเวลา/บัญชี ควรตรวจสอบข้อมูลล่าสุดจากผู้ให้บริการก่อนใช้งานจริงเชิงพาณิชย์

## 3. เปิดเว็บไซต์ในเครื่องหรือ deploy
- เปิดด้วย VS Code Live Server หรือรัน `python -m http.server 8000` ภายในโฟลเดอร์นี้ แล้วเข้า `http://localhost:8000`
- เผยแพร่ frontend ได้บน GitHub Pages, Netlify หรือ Vercel แล้วกรอก Supabase URL/key ในหน้า settings
- หากนำไปใช้จริง ให้เปลี่ยน CORS ใน `supabase/functions/generate-content/index.ts` จาก `*` เป็นโดเมนเว็บไซต์จริง

## ข้อจำกัด/สิ่งที่ควรทำต่อ
- รูปสินค้าใช้ image URL; ยังไม่ได้ทำระบบอัปโหลดไฟล์เข้าคลัง Supabase Storage
- ยังไม่มีระบบจำกัดจำนวนครั้งเรียก AI, รีเซ็ตรหัสผ่านผ่าน UI, แจ้งเตือนอัตโนมัติ หรือเผยแพร่โพสต์ไปยัง social platform
- ตรวจสอบสรรพคุณ ราคา โปรโมชัน และข้อความจาก AI ก่อนเผยแพร่ทุกครั้ง
- อย่าเปิดเผย Gemini API key หรือ Supabase service role key
