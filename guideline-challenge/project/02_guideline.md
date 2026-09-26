# Annotation guideline â€” TODO tĂªn bĂ i toĂ¡n

**Version:** v2

<!--
v0 = chÆ°a cĂ³ báº£n nhĂ¡p. Äá»•i dĂ²ng Version á»Ÿ trĂªn thĂ nh v1 khi xong báº£n nhĂ¡p Ä‘áº§u, v2 sau calibration, v3 sau blind
handoff; má»—i láº§n tÄƒng version ghi má»™t dĂ²ng vĂ o 08_revision_log.md. `make freeze` Ä‘Ă²i v2 trá»Ÿ lĂªn.

File nĂ y lĂ  thá»© nhĂ³m peer nháº­n nguyĂªn vÄƒn trong blind pack vĂ  lĂ  Guide dĂ¡n vĂ o CVAT. Peer KHĂ”NG nháº­n
edge_case_cards.md, gold_decisions.csv hay sample_pack.csv. Rule nĂ o peer cáº§n biáº¿t pháº£i náº±m á»Ÿ Ä‘Ă¢y.
No hidden rules: rule chá»‰ giáº£i thĂ­ch báº±ng miá»‡ng thĂ¬ coi nhÆ° khĂ´ng tá»“n táº¡i.
VĂ­ dá»¥ trong guideline chá»‰ dĂ¹ng áº£nh split example hoáº·c calibration, khĂ´ng dĂ¹ng áº£nh blind.
-->

## 1. Objective + scope

TODO â€” label Ä‘á»ƒ lĂ m gĂ¬; object/region nĂ o trong scope, cĂ¡i nĂ o ngoĂ i scope.

## 2. Annotation unit

TODO â€” image, frame hay track? Instance hay region? Khi nĂ o má»™t object Ä‘Æ°á»£c tĂ­nh lĂ  instance má»›i?

## 3. Geometry rule

TODO â€” rectangle / polyline / polygon; tight, visible hay amodal; Ä‘áº·t Ä‘iá»ƒm tháº¿ nĂ o; endpoint á»Ÿ Ä‘Ă¢u; tolerance.

## 4. Taxonomy

TODO â€” class hierarchy; cĂ¡i gĂ¬ lĂ  class, cĂ¡i gĂ¬ lĂ  attribute; allowed values; default vĂ  khi nĂ o dĂ¹ng `unknown`.
Báº£ng Ä‘áº§y Ä‘á»§ á»Ÿ `03_ontology_and_cvat_setup.md` â€” hai nÆ¡i pháº£i khá»›p nhau.

## 5. Inclusion / exclusion

TODO â€” trÆ°á»ng há»£p báº¯t buá»™c label; trÆ°á»ng há»£p ignore.

## 6. Visibility / occlusion

TODO â€” bá»‹ che má»™t pháº§n, bá»‹ cáº¯t mĂ©p áº£nh, nhá»/xa, pháº£n chiáº¿u, loĂ¡, Ä‘á»™ tin cáº­y tháº¥p.

## 7. Ambiguity / escalation

TODO â€” khi nĂ o LABEL / IGNORE / UNKNOWN / ESCALATE khi báº±ng chá»©ng khĂ´ng Ä‘á»§. Ghi rĂµ **thá»ƒ hiá»‡n má»—i quyáº¿t Ä‘á»‹nh trong
CVAT báº±ng cĂ¡ch nĂ o** (attribute, giĂ¡ trá»‹, tagâ€¦), Ä‘á»ƒ quyáº¿t Ä‘á»‹nh Ä‘Ă³ nhĂ¬n tháº¥y Ä‘Æ°á»£c trong file export.

## 8. Temporal rule

TODO â€” náº¿u lĂ  video/track: track báº¯t Ä‘áº§u/káº¿t thĂºc khi nĂ o, attribute nĂ o mutable, xá»­ lĂ½ chuyá»ƒn tráº¡ng thĂ¡i vĂ  bá»‹ che
ngáº¯n. Task áº£nh tÄ©nh ghi "KhĂ´ng Ă¡p dá»¥ng â€” task áº£nh tÄ©nh".

## 9. Examples

TODO â€” positive, negative vĂ  edge case, má»—i vĂ­ dá»¥ cĂ³ sample_id (split example/calibration) vĂ  expected output.

| sample_id | Tháº¥y gĂ¬ | Expected output | Rule Ă¡p dá»¥ng |
|---|---|---|---|
| TODO | TODO | TODO | TODO |

## 10. Common mistakes

TODO â€” nhá»¯ng lá»—i reviewer cĂ³ kháº£ nÄƒng gáº·p nhiá»u nháº¥t vĂ  cĂ¡ch trĂ¡nh.
