<div align="center">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/5e/Cassandra_logo.svg/2560px-Cassandra_logo.svg.png" width="150">
    <h1>Nodetool Command</h1>
</div>

| คำสั่ง                       | คำอถิบาย                                                                                                                                          |
|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| nodetool status              | แสดงสถานะของคลัสเตอร์ Cassandra                                                                                                                   |
| nodetool info                | แสดงข้อมูลเกี่ยวกับคลัสเตอร์ Cassandra                                                                                                            |
| nodetool status <node>       | แสดงสถานะของโหนด Cassandra ที่กำหนด                                                                                                               |
| nodetool info <node>         | แสดงข้อมูลเกี่ยวกับโหนด Cassandra ที่กำหนด                                                                                                        |
| nodetool version             | แสดงเวอร์ชันของ Cassandra                                                                                                                         |
| nodetool ring                | แสดงโครงสร้างวงแหวนของคลัสเตอร์ Cassandra                                                                                                         |
| nodetool decommission <node> | ถอดโหนด Cassandra ออกจากคลัสเตอร์                                                                                                                 |
| nodetool repair <node>       | คำสั่งนี้ใช้ทำการซ่อมแซมโหนด Cassandra ที่กำหนด คำสั่งนี้จะใช้ตรวจสอบข้อมูลในโหนดที่กำหนดและทำการซ่อมแซมหากพบข้อผิดพลาด                           |
| nodetool cleanup <node>      | คำสั่งนี้ใช้ทำการล้างข้อมูลที่ไม่จำเป็นออกจากโหนด Cassandra ที่กำหนด คำสั่งนี้จะใช้ลบข้อมูลแคชและข้อมูลชั่วคราวออกจากโหนดที่กำหนด                 |
| nodetool flush <keyspace>    | คำสั่งนี้ใช้ล้างแคชของคีย์สเปซที่กำหนด แคชของคีย์สเปซเป็นข้อมูลที่ถูกเก็บไว้ในหน่วยความจำของโหนด Cassandra คำสั่งนี้จะใช้ลบแคชของคีย์สเปซที่กำหนด |
| nodetool compact <keyspace>  | คำสั่งนี้ใช้ทำการบีบอัดข้อมูลของคีย์สเปซที่กำหนด การบีบอัดข้อมูลจะช่วยเพิ่มประสิทธิภาพในการเข้าถึงข้อมูลของคีย์สเปซ                               |
| nodetool describecluster     | แสดงข้อมูลเกี่ยวกับคลัสเตอร์ของ Cassandra.                                                                                                        |
| nodetool snapshot            | สร้าง snapshot ของข้อมูล.                                                                                                                         |
| nodetool cfstats             | แสดงสถิติของ Column Family.                                                                                                                       |
| nodetool disablegossip       | ปิดการสื่อสารระหว่างโหนด.                                                                                                                         |
| nodetool enablegossip        | เปิดการสื่อสารระหว่างโหนด.                                                                                                                        |
| nodetool drain               | ใช้เพื่อแน่ใจว่าโหนดปิดลงอย่างสมบูรณ์.                                                                                                            |
| nodetool repair -pr          | ทำการ repair เฉพาะส่วนของข้อมูลที่อยู่ใน partition ที่ไม่สมดุล.                                                                                   |
