# Build Rules / Implementation Rules — Working Correction Set

Status: **PHASE 5 CONTROLLED WORKING DOCUMENT**

1. `original/` adalah baseline immutable dan tidak menjadi target edit.
2. Implementasi wajib mengikuti Final Business Decision Register → PRD → Contracts → Architecture → Specifications.
3. Satu persistent business entity hanya memiliki satu authoritative owner.
4. Jangan membuat duplicate model/service untuk konsep yang sudah memiliki owner.
5. Role/Permission tidak boleh digunakan sebagai commercial entitlement.
6. Payment success tidak boleh langsung dianggap fulfillment success.
7. Cross-domain state tidak boleh diubah melalui direct database mutation.
8. Retry/replay wajib idempotent.
9. Provider timeout/ambiguous success wajib melalui reconciliation.
10. UI tidak boleh mengarang state yang tidak dikonfirmasi backend.
11. Registry hanya discovery/governance; semantic authority tetap domain contract.
12. UTC digunakan sebagai canonical platform clock.
13. Security, tenant isolation, dan authorization tidak boleh dibypass oleh configuration.
14. Setiap vertical slice wajib memiliki trace: business decision → PRD → owner → contract → state → API/event → UI → operations → acceptance criteria.
15. Jika spesifikasi tidak menentukan behavior, jangan mengarang; tandai sebagai blocking specification gap sebelum implementasi.

## Concept Lock
Tidak boleh menambah, menghapus, atau mengubah konsep produk selama implementasi tanpa change-control terhadap Final Business Decision Register.
