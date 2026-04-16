# 璺ㄦā鍧楀洖鍐欐竻鍗?> 褰撳墠瑕嗙洊鑼冨洿锛氬凡鍒嗘瀽鐨勬牳蹇冧氦鏄撻摼妯″潡锛涘熀浜?commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`锛屾棩鏈燂細2026-03-31

## 璇存槑
- 杩欓噷鍙褰曗€滅湡瀹炲啓鎿嶄綔鈥濓紝涓嶈褰曠函鏌ヨ渚濊禆銆?- 涓€鏉″啓鎿嶄綔鍙兘鍚屾椂褰卞搷涓昏〃銆佹槑缁嗚〃鍜岀姸鎬佸瓧娈碉紱鍚庣画鏀归€犳椂蹇呴』鎶婂悓涓€琛屽綋鎴愪竴涓暣浣撶湅銆?
## 鍥炲啓鎬昏〃
| 鏉ユ簮妯″潡 | 瑙﹀彂鍔ㄤ綔 | 鐩爣琛?| 鍐欏叆瀛楁 | 褰卞搷鎽樿 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|------|
| 瀹㈢暀瀛?| `createCustomerRetain` | `wh_customer_order` | 璁㈠崟涓昏〃鏂板鍙婃眹鎬诲瓧娈?| 鍒涘缓瀹㈢暀瀛樻椂鍚屾鍒涘缓瀹㈡埛璁㈠崟 | [customerretain.md](/D:/ws/code/wms-api/docs/business/customerretain.md) |
| 瀹㈢暀瀛?| `createCustomerRetain/updateCustomerRetain` | `wh_customer_order_item` | 鏂板/鏇存柊/鍒犻櫎璁㈠崟鏄庣粏 | 閲嶅缓瀹㈢暀瀛樹笌璁㈠崟鏄庣粏鐨勪竴鑷存€?| [customerretain.md](/D:/ws/code/wms-api/docs/business/customerretain.md) |
| 瀹㈢暀瀛?| `cancel` | `wh_customer_retain` | `status/end_time/day_count` | 鐣欏瓨鍙栨秷骞剁粨鏉熻鏃?| [customerretain.md](/D:/ws/code/wms-api/docs/business/customerretain.md) |
| 瀹㈢暀瀛?| `mergeRetain/splitRetain/migrateRetain` | `wh_customer_order` / `wh_customer_order_item` | 瀹㈡埛銆佹眹鎬诲瓧娈点€佹槑缁嗙粦瀹氬叧绯?| 鍚堝苟鎷嗗垎杩佺Щ鏃堕噸缁戣鍗曚晶鏁版嵁 | [customerretain.md](/D:/ws/code/wms-api/docs/business/customerretain.md) |
| 浜у搧鍖?| `createPack/updatePack` | `wh_customer_order_item` | `pack_id/local_status` | 瀹㈢暀瀛樿鎵撳寘鍚庯紝璁㈠崟鏄庣粏杩涘叆寮€鍗曟€?| [pack.md](/D:/ws/code/wms-api/docs/business/pack.md) |
| 浜у搧鍖?| `createPack` | `wh_customer_retain` | `pack_no/status` | 瀹㈢暀瀛樺畬鏁存墦鍖呭悗杩涘叆 `ONGOING` | [pack.md](/D:/ws/code/wms-api/docs/business/pack.md) |
| 浜у搧鍖?| `updatePack/obsolete` | `wh_customer_retain` | `pack_no/status` | 鎷嗙粦鎴栦綔搴熶骇鍝佸寘鏃堕噴鏀惧鐣欏瓨 | [pack.md](/D:/ws/code/wms-api/docs/business/pack.md) |
| 浜у搧鍖?| `genConfirmTransfer` | `wh_pack` | `transferBillId/transferBillNo/transferType/transferStatus` | 瀹℃牳璋冩嫧鍚庢妸浜у搧鍖呯疆涓鸿皟鎷ㄤ腑 | [pack.md](/D:/ws/code/wms-api/docs/business/pack.md) |
| 璋冩嫧鍗?| `confirm/received/rejected/backReceived/backRejected/infirm/discard` | `wh_pack` | `transferStatus/transferBillId/transferBillNo/transferType` | 鍗曚欢璋冩嫧鍚勯樁娈靛洖鍐欐簮浜у搧鍖?| [transferbill.md](/D:/ws/code/wms-api/docs/business/transferbill.md) |
| 鎴愬搧璋冩嫧鍗?| `confirm/received/rejected/backReceived/backRejected/infirm/discard` | `wh_pack` | `transferStatus/transferBillId/transferBillNo/transferType` | 鎴愬搧璋冩嫧鍚勯樁娈靛洖鍐欐簮浜у搧鍖?| [goodstransferbill.md](/D:/ws/code/wms-api/docs/business/goodstransferbill.md) |
| 鎴愬搧璋冩嫧鍗?| `doOriginBillStatus` | `wh_sale_bill` | `transferStatus/transferBillId/transferBillNo/transferType` | 婧愬崟鎹槸缁撶畻鍗曟椂锛岃繛甯﹀洖鍐欑粨绠楀崟璋冩嫧瀛楁 | [goodstransferbill.md](/D:/ws/code/wms-api/docs/business/goodstransferbill.md) |
| 鍑哄簱缁撶畻鍗?| `createSaleBill/updateSaleBill` | `wh_customer_order_item` | `local_status = SETTLEMENT` | 杩涘叆缁撶畻涓?| [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| 鍑哄簱缁撶畻鍗?| `createSaleBill/updateSaleBill` | `wh_pack` | `sale_bill_id/sale_bill_no` | 鎶婁骇鍝佸寘鎸傚埌褰撳墠缁撶畻鍗?| [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| 鍑哄簱缁撶畻鍗?| `confirm` | `wh_pack` | `pack_status = RECEIVED` | 瀹℃牳鍚庝骇鍝佸寘杩涘叆宸茬粨绠楁€?| [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| 鍑哄簱缁撶畻鍗?| `confirm` | `wh_customer_order_item` | `local_status = BILLED` | 璁㈠崟鏄庣粏杩涘叆宸插紑绁?宸茬粨绠?| [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| 鍑哄簱缁撶畻鍗?| `unConfirm` | `wh_pack` | `pack_status = CONFIRM` | 鍙嶅杩樺師浜у搧鍖?| [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| 鍑哄簱缁撶畻鍗?| `unConfirm` | `wh_customer_order_item` | `local_status = SETTLEMENT` | 鍙嶅杩樺師璁㈠崟鏄庣粏 | [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| 鍑哄簱缁撶畻鍗?| `obsolete` | `wh_customer_order_item` | `local_status = BILLING` | 浣滃簾缁撶畻鍗曞悗鍥為€€鍒板紑鍗曟€?| [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| 鍑哄簱缁撶畻鍗?| `actualDelivered` | `wh_customer_retain` | 缁撶暀瀛樼浉鍏冲瓧娈?| 瀹為檯鍙戣揣鍚庣粨鐣欏瓨 | [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| 鍑哄簱缁撶畻鍗?| `actualDelivered/orderRedo` | `wh_customer_order` | 鍑鸿揣淇℃伅鍥炲啓/鍥炴粴 | 瀹為檯鍙戣揣鍜岄噸鍋氫細鏀硅鍗曞嚭璐т晶瀛楁 | [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| 鍑鸿揣鍗?| `ship/reship` | `wh_customer_order` | `outPpOrderNo` | `TYPE_B` 澶栧崟鍙戣揣鍓嶅洖鍐欏閮ㄥ崟鍙?| [deliverybill.md](/D:/ws/code/wms-api/docs/business/deliverybill.md) |
| 鍑鸿揣鍗?| `callbackYxysOrder/callbackPPOrder` | `wh_customer_order` | `order_status/out_order_status` | 鍥炶皟鎴愬姛鎴栧け璐ュ悗鏀硅鍗曞閮ㄥ嚭璐х姸鎬?| [deliverybill.md](/D:/ws/code/wms-api/docs/business/deliverybill.md) |
| 璁㈠崟鏍搁攢 | `confirm/infirm` | `wh_outsourcing_order_detail` | `totalPass* / totalBad* / totalReturn* / totalWriteoff* / writeoffDate` | 瀹℃牳鏃剁疮鍔犮€佸弽瀹℃椂鍙嶅悜鎵ｅ噺濮斿鏄庣粏绱鏍搁攢缁撴灉 | [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |
| 璁㈠崟鏍搁攢 | `confirm/infirm` | `wh_outsourcing_order` | `writeoffDate/totalDeliveryNum/totalDeliveryGramWeight/orderStatus` | 鎸夊叏閲忓澶栨槑缁嗚仛鍚堬紝鎺ㄨ繘濮斿鍗曞埌寰呮牳閿€/閮ㄥ垎鏍搁攢/鏍搁攢瀹屾瘯 | [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |
| 璁㈠崟鏍搁攢 | `confirm/infirm` | `wh_demand_order_detail` | `returnedNum/returnedWeight` | 闇€姹傚崟鏄庣粏绱鍊兼寜鈥滃悎鏍兼牳閿€閲忊€濆鍑忥紝鑰屼笉鏄寜鎬诲洖璐ч噺澧炲噺 | [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |
| 璁㈠崟鏍搁攢 | `confirm/infirm` | `wh_demand_order` | 鑱氬悎瀛楁銆乣writeoffDate` | 闇€姹傚崟澶撮€氳繃 `aggregateByDetails()` 鐢辨槑缁嗛噸鏂拌仛鍚?| [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |
| 鏀舵鍗?| `confirm/unConfirm` | `wh_shop_bank` / `wh_shop_bank_log` | 閾惰鍗′綑棰濄€侀摱琛屾棩姹囨€汇€侀摱琛屾棩蹇?| 瀹℃牳鏃舵寜鏄庣粏閾惰璐﹀彿鍏ヨ处锛屽弽瀹℃椂鍙嶅悜鍐插洖 | [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md) |
| 鏀舵鍗?| `confirm/unConfirm` | `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 閽卞寘浣欓銆侀挶鍖呮棩蹇椼€侀挶鍖呮棩姹囨€?| 瀹℃牳鏃舵寜鍘熻处鎴峰叆璐︼紝鍙嶅鏃跺弽鍚戝洖婊?| [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md) |
| 鏀舵鍗?| `divide/unDivide` | `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 澶氳处鎴烽挶鍖呭綊灞為噸鍒嗛厤 | 鍒嗚处鍏堝啿鍥炲師璐︽埛锛屽啀鍒嗛厤鍒板涓处鎴凤紱鍙栨秷鍒嗚处鍐嶆寕鍥炲師璐︽埛 | [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md) |
| 鏀舵鍗?| `divide/unDivide` | `wh_receivable_divide_bill` | `bill_no/account_id/total_amount` | 鎸佷箙鍖栧垎璐︾粨鏋滃苟鍦ㄥ彇娑堝垎璐︽椂鍒犻櫎 | [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md) |

## 楂橀闄╁叡浜〃
| 琛?| 楂橀闄╁師鍥?| 棣栬鍏虫敞妯″潡 |
|------|------|------|
| `wh_customer_order` | 琚鐣欏瓨銆佽鍗曘€佺粨绠椼€佸嚭璐у鏂规敼鍐?| 瀹㈢暀瀛樸€佸鎴疯鍗曘€佸嚭搴撶粨绠楀崟銆佸嚭璐у崟 |
| `wh_customer_order_item` | 鏈湴鐘舵€侀┍鍔ㄨ鍗曚富鐘舵€?| 瀹㈢暀瀛樸€佷骇鍝佸寘銆佸嚭搴撶粨绠楀崟 |
| `wh_customer_retain` | 鍚屾椂鍙楃暀瀛樸€佷骇鍝佸寘銆佺粨绠楀奖鍝?| 瀹㈢暀瀛樸€佷骇鍝佸寘銆佸嚭搴撶粨绠楀崟 |
| `wh_pack` | 鍚屾椂鎸傜粨绠楀崟鍜岃皟鎷ㄥ崟 | 浜у搧鍖呫€佽皟鎷ㄥ崟銆佹垚鍝佽皟鎷ㄥ崟銆佸嚭搴撶粨绠楀崟 |
| `wh_sale_bill` | 鍚屾椂鍙楃粨绠楀拰鎴愬搧璋冩嫧鍥炲啓 | 鍑哄簱缁撶畻鍗曘€佹垚鍝佽皟鎷ㄥ崟 |
| `wh_outsourcing_order` | 鏃㈠彈濮斿鐢熶骇閾剧淮鎶わ紝鍙堝彈璁㈠崟鏍搁攢鑱氬悎鏀瑰啓鐘舵€佷笌绱鍊?| 濮斿鍗曘€佽鍗曟牳閿€ |
| `wh_outsourcing_order_detail` | 璁㈠崟鏍搁攢鐩存帴绱姞/鍥為€€澶氬绱瀛楁 | 濮斿鍗曘€佽鍗曟牳閿€ |
| `wh_demand_order` | 璁㈠崟闇€姹傚崟鏈湴缁存姢锛屽悓鏃朵細琚鍗曟牳閿€鎸夋槑缁嗚仛鍚堝洖鍐?| 璁㈠崟闇€姹傚崟銆佽鍗曟牳閿€ |
| `wh_demand_order_detail` | 璁㈠崟鏍搁攢鐩存帴鏀圭疮璁″洖璐у瓧娈?| 璁㈠崟闇€姹傚崟銆佽鍗曟牳閿€ |
| `wh_shop_bank` | 鏀舵鍗曞鏍?鍙嶅鐩存帴鏀逛綑棰濆苟鍒锋柊鏃ユ眹鎬?| 鏀舵鍗?|
| `wh_wallet` | 鏀舵鍗曞鏍?鍙嶅鍜屽垎璐?鍙栨秷鍒嗚处閮戒細鏀逛綑棰?| 鏀舵鍗?|
| `wh_wallet_log` | 鏀舵鍗曞鏉″姩浣滈兘浼氬垹鏀规垨閲嶅缓鏃ュ織 | 鏀舵鍗?|
| `wh_payable_bill` | 瀹℃牳/鍙嶅涓嶄粎鏀瑰崟澶寸姸鎬侊紝杩橀┍鍔ㄩ摱琛屽拰閽卞寘鍙岃祫閲戦摼鍥炲啓 | 浠樻鍗?|
| `wh_payable_bill_detail` | 閾惰鍗″嚭璐︽寜鏄庣粏閫愭潯钀借处锛屾槑缁嗛噾棰濈洿鎺ラ┍鍔ㄩ摱琛屼綑棰濆彉鍖?| 浠樻鍗?|
| `wh_shop_bank` | 鏀朵粯娆惧鏍?鍙嶅閮戒細鏀逛綑棰濓紝浣嗘柟鍚戠浉鍙?| 鏀舵鍗曘€佷粯娆惧崟 |
| `wh_wallet` | 鏀舵鍒嗚处涓庝粯娆惧鏍?鍙嶅閮戒細鏀逛綑棰濓紝璧勯噾鏂瑰悜涓嶈兘娣风湅 | 鏀舵鍗曘€佷粯娆惧崟 |
| `wh_wallet_log` | 鏀舵閾句細鍒犻櫎/閲嶅缓鏃ュ織锛屼粯娆鹃摼浼氱洿鎺ユ柊澧炴棩蹇楋紝鏀归€犳椂瑕佸悓鏃舵牎楠屼袱绉嶆ā寮?| 鏀舵鍗曘€佷粯娆惧崟 |
| `wh_wallet` | 瀹㈡埛搴旀敹璐﹁皟鏁磋繕浼氬悓鏃舵敼鐜伴噾閽卞寘涓庢潗璐ㄩ挶鍖咃紝閽卞寘绫绘敼閫犱笉鑳藉彧鐪嬫敹浠樻閾?| 鏀舵鍗曘€佷粯娆惧崟銆佸鎴峰簲鏀惰处璋冩暣 |
| `wh_wallet_log` | 瀹㈡埛搴旀敹璐﹁皟鏁存寜鏄庣粏閫愭潯鏂板閲戦璐︿笌閲戦噸璐︽棩蹇楋紝鏃ュ織閲忎笌鍙ｅ緞閮芥洿缁?| 鏀舵鍗曘€佷粯娆惧崟銆佸鎴峰簲鏀惰处璋冩暣 |

## 浠樻鍗曡ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 瑙﹀彂鍔ㄤ綔 | 鐩爣琛?| 鍐欏叆瀛楁 | 褰卞搷鎽樿 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|------|
| 浠樻鍗?| `confirm/unConfirm` | `wh_shop_bank` / `wh_shop_bank_log` | 閾惰鍗′綑棰濄€侀摱琛屾棩姹囨€汇€侀摱琛屾棩蹇?| 瀹℃牳鏃舵寜鏄庣粏 `bankCode` 閫愭潯鎵ｅ噺閾惰鍗′綑棰濓紝鍙嶅鏃堕€愭潯鍔犲洖 | [payablebill.md](/D:/ws/code/wms-api/docs/business/payablebill.md) |
| 浠樻鍗?| `confirm/unConfirm` | `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 閽卞寘浣欓銆侀挶鍖呮棩蹇椼€侀挶鍖呮棩姹囨€?| 瀹℃牳鏃舵寜鍗曞ご `accountId` 澧炲姞鐜伴噾閽卞寘浣欓锛屽弽瀹℃椂鍙嶅悜鎵ｅ洖 | [payablebill.md](/D:/ws/code/wms-api/docs/business/payablebill.md) |
| 瀹㈡埛搴旀敹璐﹁皟鏁?| `confirm/unConfirm` | `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 閽卞寘浣欓銆侀挶鍖呮棩蹇椼€侀挶鍖呮棩姹囨€?| 瀹℃牳/鍙嶅鎸夋槑缁嗛€愭潯璋冩暣瀹㈡埛鐜伴噾閽卞寘涓庢潗璐ㄩ挶鍖咃紝涓嶇粡杩囬摱琛屽崱 | [customeradjustbill.md](/D:/ws/code/wms-api/docs/business/customeradjustbill.md) |
## 渚涘簲鍟嗗簲浠樿处璋冩暣琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 瑙﹀彂鍔ㄤ綔 | 鐩爣琛?| 鍐欏叆瀛楁 | 褰卞搷鎽樿 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|------|
| 渚涘簲鍟嗗簲浠樿处璋冩暣 | `confirm/unConfirm` | `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 閽卞寘浣欓銆侀挶鍖呮棩蹇椼€侀挶鍖呮棩姹囨€?| 瀹℃牳/鍙嶅鎸夋槑缁嗛€愭潯璋冩暣渚涘簲鍟嗙幇閲戦挶鍖呬笌鏉愯川閽卞寘锛屼笉缁忚繃閾惰鍗?| [supplieradjustbill.md](/D:/ws/code/wms-api/docs/business/supplieradjustbill.md) |

## 渚涘簲鍟嗗簲浠樿处璋冩暣琛ュ厖楂橀闄╁叡浜〃
| 琛?| 楂橀闄╁師鍥?| 棣栬鍏虫敞妯″潡 |
|------|------|------|
| `wh_supplier_adjust_bill` | 瀹℃牳/鍙嶅涓嶅彧鏀瑰崟澶寸姸鎬侊紝杩橀┍鍔ㄤ緵搴斿晢閽卞寘閲戦璐︿笌閲戦噸璐﹀洖鍐?| 渚涘簲鍟嗗簲浠樿处璋冩暣 |
| `wh_supplier_adjust_bill_detail` | 鏄庣粏鍚屾椂鎵胯浇閲戦璐︿笌閲戦噸璐﹀叆鍙ｏ紝涓€鏉℃槑缁嗗彲鑳借Е鍙戜袱娆￠挶鍖呭姩璐?| 渚涘簲鍟嗗簲浠樿处璋冩暣 |
| `wh_wallet` | 鐜板凡鍚屾椂琚敹娆俱€佷粯娆俱€佸鎴峰簲鏀惰皟鏁淬€佷緵搴斿晢搴斾粯璋冩暣鍥涙潯璧勯噾閾炬敼鍐?| 鏀舵鍗曘€佷粯娆惧崟銆佸鎴峰簲鏀惰处璋冩暣銆佷緵搴斿晢搴斾粯璐﹁皟鏁?|
| `wh_wallet_log` | 鏂板渚涘簲鍟嗗簲浠樿皟鏁村悗锛岄挶鍖呮棩蹇楁棦鎵胯浇瀹㈡埛渚т篃鎵胯浇渚涘簲鍟嗕晶璋冩暣鍙ｅ緞 | 鏀舵鍗曘€佷粯娆惧崟銆佸鎴峰簲鏀惰处璋冩暣銆佷緵搴斿晢搴斾粯璐﹁皟鏁?|
## 璋冭处鍗曡ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 瑙﹀彂鍔ㄤ綔 | 鐩爣琛?| 鍐欏叆瀛楁 | 褰卞搷鎽樿 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|------|
| 璋冭处鍗?| `confirm/unConfirm` | `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 閽卞寘浣欓銆侀挶鍖呮棩蹇椼€侀挶鍖呮棩姹囨€?| 瀹℃牳/鍙嶅鎸夊崟澶存眹鎬诲€硷紝鍦ㄨ皟鍑鸿处鎴蜂笌璋冨叆璐︽埛涔嬮棿鎴愬璋冩暣鐜伴噾閽卞寘涓庢潗璐ㄩ挶鍖?| [accountadjustbill.md](/D:/ws/code/wms-api/docs/business/accountadjustbill.md) |

## 璋冭处鍗曡ˉ鍏呴珮椋庨櫓鍏变韩琛?| 琛?| 楂橀闄╁師鍥?| 棣栬鍏虫敞妯″潡 |
|------|------|------|
| `wh_account_adjust_bill` | 瀹℃牳/鍙嶅涓嶅彧鏀瑰崟澶寸姸鎬侊紝杩橀┍鍔ㄨ皟鍑?璋冨叆璐︽埛鎴愬鍔ㄨ处 | 璋冭处鍗?|
| `wh_account_adjust_bill_detail` | 鏄庣粏姹囨€诲€煎喅瀹氬崟澶磋皟璐﹂噾棰濆拰閲戦噸锛岀簿搴﹀彉鍖栦細鐩存帴浼犲鍒伴挶鍖呴摼 | 璋冭处鍗?|
| `wh_wallet` | 鐜板凡鍚屾椂琚敹娆俱€佷粯娆俱€佸鎴峰簲鏀惰皟鏁淬€佷緵搴斿晢搴斾粯璋冩暣銆佽皟璐﹀崟浜旀潯璧勯噾閾炬敼鍐?| 鏀舵鍗曘€佷粯娆惧崟銆佸鎴峰簲鏀惰处璋冩暣銆佷緵搴斿晢搴斾粯璐﹁皟鏁淬€佽皟璐﹀崟 |

## 盈亏调整单补充回写
| 来源模块 | 触发动作 | 目标表 | 写入字段 | 影响摘要 | 依据文档 |
|------|------|------|------|------|------|
| 盈亏调整单 | `confirm/unConfirm` | 成品库存相关表 | 库存件数、金重等库存余额字段 | `3601` 审核/反审时按明细正负值直接增减成品库存 | [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md) |
| 盈亏调整单 | `confirm/unConfirm` | `wh_material_stock` / `wh_material_stock_log` | 原料库存余额、毛重、日志字段 | `3602` 审核/反审时先按 `materialNo` 聚合，再统一回写原料库存与库存日志 | [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md) |

## 盈亏调整单补充高风险共享表
| 表 | 高风险原因 | 首要关注模块 |
|------|------|------|
| `wh_profit_loss_adjust_bill` | 审核/反审不只改单头状态，还驱动后续库存服务按不同 `bizType` 分流写回 | 盈亏调整单 |
| `wh_profit_loss_adjust_detail` | 明细正负件数/金重直接决定盘盈盘亏方向，空值或精度问题会直接传导到库存链 | 盈亏调整单 |
| `wh_material_stock` | 现已不仅受原料收发料影响，也会被盈亏调整单直接改写 | 盈亏调整单、原料流转模块 |
| `wh_material_stock_log` | 盈亏调整单会显式生成库存日志，后续核账要同时看库存余额与日志链 | 盈亏调整单、原料流转模块 |

## 客户来料单补充回写
- 审核/反审会同时回写三条链：原料库存、客户材质钱包、客户现金钱包。
- 客户材质钱包主账吃的是 `wh_material_inbound_settle.settleWeight` 按 `materialNo + settleClass` 聚合结果，不是明细 `convertWeight` 直接落账。
- 现金钱包单独吃单头 `totalSettleMoney`，且方向与材质钱包主账相反。
- 若结算明细含 `CLOSING_PRICE(02)`，还会再做一笔材质钱包反向冲回。

## 供应商来料单补充回写
- 审核/反审同样会回写原料库存与供应商钱包，但钱包主账口径和客户来料不同。
- 供应商来料先按明细汇总钱包口径，再经 `InboundSupplierFactory` 按业务类型过滤真正要动的钱包项。
- `2201` 采购入库：现金大于 0 时只走现金钱包，否则只走材质钱包。
- `2202` 提纯入库：材质钱包和现金钱包都可能一起变动。

## 成品入库单补充回写
- 审核/反审会同时回写成品库存和供应商钱包。
- 材质钱包主账吃明细 `goldWeight` 合计，现金钱包主账吃明细 `cmtFee` 合计。
- `0402/0403` 还会继续回写半成品回流累计与供应商领料台账。
- 作废会解除 `wh_outsourcing_order_detail.convertInboundNo` 挂接。

## 客户出料单补充回写
- 审核/反审会同时回写两条链：原料库存、客户钱包。
- `2101-客户买料` 只保留现金钱包。
- `2102-退客户料` 只保留材质钱包。

## 供应商出料单补充回写
- 审核/反审同样会回写原料库存和供应商钱包。
- `2301-发料兑料` 只动材质钱包。
- `2302-发料提纯` 同时动材质钱包和现金钱包。
- `2303-卖料出库` 只动现金钱包。

## 瀹㈡埛閫€璐у崟琛ュ厖鍥炲啓
- 瀹℃牳/鍙嶅浼氬悓鏃跺洖鍐欏崟浠?鎴愬搧搴撳瓨銆佸鎴烽挶鍖呭拰缁翠慨娴佽浆浠撲綅銆?- 涓昏处鎸?`wh_return_settle` 鐨?`materialNo + settleClass + settleWeight` 鑱氬悎鍥炲啓瀹㈡埛鏉愯川閽卞寘锛屽苟鐢?`totalSettleMoney` 鍙嶅悜鍥炲啓鐜伴噾閽卞寘銆?- 鑻ュ瓨鍦?`CLOSING_PRICE(02)`锛岃繕浼氶澶栧仛涓€绗旀潗璐ㄩ挶鍖呭弽鍚戝啿鍥炪€?
## 鎴愬搧閫€鍘傜櫥璁板崟琛ュ厖鍥炲啓
- 瀹℃牳/鍙嶅鏈韩涓嶅洖鍐欏簱瀛樺拰閽卞寘銆?- `0503-缁翠慨鐧昏` 瀹℃牳鏃朵細绱姞 `wh_maintain_bill_detail.recordNum`锛屽弽瀹℃椂鍥炴粴锛屼綔搴熸椂閲婃斁 `recordId` 鍗犵敤銆?- 琚垚鍝侀€€鍘傚崟寮曠敤鍚庯紝`wh_goods_reject_record.rejectBillId/outOfWare` 浼氱敱閫€鍘傛墽琛屽崟渚ф帹杩涖€?
## 鎴愬搧閫€鍘傚崟琛ュ厖鍥炲啓
- 瀹℃牳/鍙嶅浼氬悓鏃跺洖鍐欐垚鍝佸簱瀛樺拰渚涘簲鍟嗛挶鍖呫€?- `0601-鎴愬搧閫€鍘俙 浼氬洖鍐欓€€鍘傜櫥璁版睜 `rejectBillId/outOfWare`銆?- `0602-渚涘簲鍟嗛鏂檂 浼氬洖鍐欏崐鎴愬搧璋冩嫧鍗?`rejectBillNo`锛屽苟鍚屾 `SemisSupplierBreakdownApi` 渚涘簲鍟嗛鏂欏彴璐︺€?- 浣滃簾涓嶄細鍙敼鍗曞ご鐘舵€侊紝杩樹細瑙ｉ櫎鐧昏姹犳垨鍗婃垚鍝佽皟鎷ㄦ睜鐨勬潵婧愭寕鎺ャ€?

## 鎴愬搧杞枡鍗曡ˉ鍏呭洖鍐?- 瀹℃牳/鍙嶅浼氬悓鏃跺洖鍐欏師鏂欏簱瀛樺拰鎴愬搧搴撳瓨銆?- 鍘熸枡搴撳瓨缁熶竴鎸夊崟澶?`materialNo + destWareId` 鍏ュ簱鎴栧洖婊氾紝涓嶆槸鏄庣粏閫愯鎸囧畾鍘熸枡缂栫爜銆?- 甯?`itemNo` 鐨勬槑缁嗚繕浼氱户缁笅閽?`RecycleBillService.doSubmit(...)`锛屽悓姝ラ┍鍔ㄥ崟浠惰浆鏂欑姸鎬佹祦銆?

## 鍗曚欢鍏ュ簱鍗曡ˉ鍏呭洖鍐?- `wh_item`锛氬鏍告壒閲忓缓鍗曚欢锛屽弽瀹＄墿鐞嗗垹闄ゅ崟浠躲€?- `wh_item_log`锛氬鏍稿啓鍏ュ簱鏃ュ織锛屽弽瀹″啓鍥炴粴鏃ュ織銆?- `wh_sku_stock`锛氬鏍稿悓鏃跺仛鈥滃崟浠跺簱瀛樺叆 + 鎴愬搧搴撳瓨鍑衡€濓紝鍙嶅鏁存潯鍥炴粴銆?- `wh_demand_order_detail / wh_demand_order`锛氬鏍?鍙嶅鎸?`demandDetailId` 鍥炲啓 `entryBillNum/entryBillWeight` 骞堕噸鏂拌仛鍚堣鍗曞ご銆?- `wh_maintain_bill_detail`锛氬鏍?鍙嶅鍥炲啓 `itemInNum/newItemNo/newGoldWeight/newWeight/newGrossWeight` 涓庢楠や粨浣嶃€?- `wh_item_accessories / wh_item_part`锛氬鏍稿鍒跺崟浠惰緟鏂欙紝鍙嶅娓呯悊銆?

## 鍗曚欢璋冩暣鍗曡ˉ鍏呭洖鍐?- `wh_item`锛氬鎵归€氳繃鍚庣洿鎺ヨ鐩栧師鍗曚欢鐨勬寮忋€佹ā鍙枫€侀噸閲忋€佸伐璐广€佸垎绫汇€佹潗璐ㄧ瓑灞炴€с€?- `wh_item_log`锛氬鎵归€氳繃鍚庝负姣忔潯鏄庣粏鍐欌€滄棫浠?OUT + 鏂颁欢 IN鈥濆弻鏃ュ織銆?- `wh_sku_stock`锛氬鎵归€氳繃鍚庡悓鏃跺仛鍗曚欢搴撳瓨 `moveStock` 鍜屾垚鍝佸簱瀛樺樊棰濊皟鏁淬€?- `wh_maintain_bill_detail`锛氳嫢鎸傜淮淇槑缁嗭紝鍒欏悓姝ユ洿鏂?`newItemId/newItemNo/newGoldWeight/newGrossWeight/newWeight`銆?- `wh_item_accessories`锛氬鎵归€氳繃鍚庡厛鍒犳棫杈呮枡锛屽啀鎸夎皟鏁村崟鏄庣粏閲嶅缓鍗曚欢杈呮枡銆?
## 鍗曚欢浣滃簾鍗曡ˉ鍏呭洖鍐?
- `wh_item`锛氬鎵归€氳繃鏃舵妸鍗曚欢鐘舵€佷粠 `ON_WARE` 鏀逛负 `DISCARD`锛涘弽瀹℃椂鍐嶄粠 `DISCARD` 鍥炴粴涓?`ON_WARE`銆?- `wh_item_log`锛氬鏍稿拰鍙嶅閮戒細琛ヤ竴鏉′綔搴?鍥炴粴鏃ュ織锛屼繚鐣欏崟浠剁姸鎬佸彉鍖栬建杩广€?- `wh_sku_stock`锛氬鏍告椂鎵ц鈥滃崟浠跺簱瀛樺嚭搴?+ 鎴愬搧搴撳瓨鍥炴敹鈥濓紱鍙嶅鏃舵暣鏉″弽鍚戝洖婊氥€?
## 鍗曚欢鐩樼偣鍗曟搷浣滆ˉ鍏呭洖鍐?
- `wh_item_inventory_bill_item`锛氬缓鍗曟椂鏁存壒鍐荤粨璐﹂潰鍗曚欢蹇収锛屾壒娆￠噰闆嗘椂鍐嶆爣璁扳€滃凡鐩樺埌鈥濇垨鎻掑叆鈥滅洏鐩堚€濊褰曘€?- `wh_item_inventory_bill_batch`锛氭壙鎺ユ瘡娆￠噰闆嗘壒娆＄殑鐩樺埌鏁板拰鐩樼泩鏁帮紱浣滃簾鎵规鏃朵細鎶婃湰鎵归噰闆嗙粨鏋滄暣鎵规挙閿€銆?- `wh_item_inventory_bill_stock`锛氬缓鍗曠灛闂存寜浠撳簱/澶х被/鏉愯川缁村害澶囦唤搴撳瓨姹囨€伙紝浣滀负鐩樼偣鍩虹嚎鍓湰銆?
## 搴撲綅璋冩暣鍗曡ˉ鍏呭洖鍐?
- `wh_item`锛氬鏍告椂鎶婂崟浠?`localId` 浠庡師搴撲綅鏀瑰埌鐩爣搴撲綅锛涘弽瀹℃椂鍐嶆寜鏄庣粏鍥炴粴鍒?`sourLocalId`銆?- `wh_sku_stock`锛氬鏍?鍙嶅閮介€氳繃 `moveStock(...)` 璋冩暣搴撲綅缁村害搴撳瓨鍒嗗竷锛屼笉鏀瑰崟浠舵暟閲忓彛寰勶紝鍙惉浣嶇疆銆?- `wh_item_log`锛氬缓鍗曢攣瀹氥€佹敼鍗曠Щ闄よВ閿併€佸鏍歌В閿併€佷綔搴熻В閿併€佸弽瀹￠噸鏂伴攣瀹氶兘浼氳惤鏃ュ織銆?
## 鍗曚欢鏄庣粏琛ュ厖鍥炲啓

- `wh_item`锛氭槸鍗曚欢鍏ュ簱銆佽皟鏁淬€佷綔搴熴€佸簱浣嶃€侀€€璐с€佽浆鏂欑瓑澶氭潯鎵ц閾惧叡鍚岃惤鐐广€?- `wh_item_log`锛氬崟浠跺垪琛ㄣ€佽储鍔¤鍥惧拰杩愮淮鍔ㄤ綔閮戒細渚濊禆瀹冭ˉ鈥滄渶杩戝姩浣?鏈€鍚庡崟鍙?鏈€鍚庢椂闂粹€濄€?- `wh_item_accessories`锛氭櫘閫氬垪琛ㄣ€佽储鍔″垪琛ㄥ拰瀵煎嚭閮戒細鎶婂崟浠惰緟鏂欒ˉ鍏ュ睍绀鸿鍥俱€?
## 鐩樼偣绉伴噸鍗曡ˉ鍏呭洖鍐?
- `wh_inventory_weighing_detail`锛氬鏍告椂閫愭潯鍥炲啓 `profitLossNum/profitLossGoldWeight`锛屽弽瀹℃椂鏁存壒娓呯┖銆?- `wh_inventory_weighing_bill`锛氬鏍告椂姹囨€诲洖鍐欐€荤泩浜忎欢鏁?閲戦噸锛屽弽瀹℃椂閲嶇疆涓?0銆?- `wh_inventory_weighing_bill_stock`锛氬綋鍓嶅彧鍦ㄦ垚鍝佺О閲嶅鏍告椂鍒濆鍖栬处闈㈠簱瀛樺浠姐€?

## 缁翠慨鍗曡ˉ鍏呭洖鍐?- 缁翠慨鍗?-> 鍗曚欢琛細瀹℃牳鏃舵寕涓?`maintainBillId/maintainBillDetailId/maintainBillNo/maintainBillDetailReplyStatus`锛屽弽瀹℃椂鏁寸粍娓呯┖銆?- 缁翠慨鍗?-> 瀹㈡埛閫€璐ф槑缁嗭細瀹㈡埛缁翠慨瀹℃牳/鍙嶅鏃跺鍑?`wh_return_detail.maintainNum`锛屽苟鏍￠獙绱缁翠慨鏁颁笉鑳借秴杩囬€€璐ф暟銆?- 缁翠慨鍗?-> 閽卞寘锛氬鎴风淮淇鏍告椂鎸夌淮淇粨绠楁槑缁嗗仛 `DEPOSIT`锛屽弽瀹℃椂鍋?`DEBT`锛涘叾涓?`CLOSING_PRICE(02)` 杩樹細鍗曠嫭鍋氫竴绗斿弽鍚戝啿鍥炪€?- 缁翠慨鍗?-> 璋冩嫧鍗?鎴愬搧璋冩嫧鍗曪細涓€閿皟鎷ㄦ椂鐩存帴娲剧敓鎵ц鍗曞苟绔嬪埢瀹℃牳锛岀淮淇崟鑷韩鍙洖鍐?`transferStatus`銆?- 缁翠慨鍗?-> 鎴愬搧搴撳瓨/鍗曚欢搴撳瓨锛氫笉鍚岀淮淇垎鏀細缁?`goodsHandle()/itemHandle()` 缁х画涓嬮捇搴撳瓨鍔ㄤ綔锛屼笉鑳藉彧鐪嬪崟澶寸姸鎬佸彉鏇淬€?

## 缁翠慨鍏ュ簱鍗曡ˉ鍏呭洖鍐?- 缁翠慨鍏ュ簱鍗?-> 缁翠慨鍗曟槑缁嗭細瀹℃牳/鍙嶅鏃跺鍑?`inboundNum/inNum/repareFee`锛屽苟鍥炲啓 `newItemId/newItemNo/newGoldWeight/newGrossWeight/newWeight` 涓?`replyStatus`銆?- 缁翠慨鍏ュ簱鍗?-> 缁翠慨鍗曞ご锛氶€氳繃 `maintainBillService.updateTotalColumn(...)` 閲嶇畻 `replyStatus` 涓庣疮璁″瓧娈点€?- 缁翠慨鍏ュ簱鍗?-> 缁翠慨鍑哄簱鍗曪細瀹℃牳/鍙嶅鏃跺鍑忕淮淇嚭搴撴槑缁?`inNum`锛涗綔搴熸椂瑙ｉ櫎 `pushMaintainInBillDetailId` 鑽夌鎸傛帴銆?- 缁翠慨鍏ュ簱鍗?-> 鍗曚欢琛細鍗曚欢瀹℃牳鏃舵仮澶嶄负 `ON_WARE` 骞跺垏鍒板綋鍓嶄粨浣嶏紝鍙嶅鏃舵仮澶嶄负 `MAINTAIN` 鍜屽師濮嬩粨浣嶃€?- 缁翠慨鍏ュ簱鍗?-> 閽卞寘锛氬鏍?鍙嶅鏃舵寜閲戦噸鍜屽伐璐瑰悓鏃跺洖鍐欎緵搴斿晢鏉愯川閽卞寘涓庣幇閲戦挶鍖呫€?

## 缁翠慨鍑哄簱鍗曡ˉ鍏呭洖鍐?- 缁翠慨鍑哄簱鍗?-> 缁翠慨鍗曟槑缁嗭細瀹℃牳/鍙嶅鏃跺鍑?`outNum`锛屽苟娓呯┖/鎭㈠涓婁竴杞淮淇叆搴撶粨鏋滃瓧娈?`inboundNum/repareFee/newItem.../last...`銆?- 缁翠慨鍑哄簱鍗?-> 缁翠慨鍗曞ご锛氶€氳繃 `maintainBillService.updateTotalColumn(...)` 閲嶇畻缁翠慨杩涘害銆?- 缁翠慨鍑哄簱鍗?-> 閫€鍘傜櫥璁版睜锛氳崏绋?瀹℃牳/鍙嶅鏃舵寔缁洖鍐?`wh_goods_reject_record.outOfWare` 涓庢槑缁?`maintainOutNum`銆?- 缁翠慨鍑哄簱鍗?-> 鍗曚欢琛細瀹℃牳鏃舵妸鍗曚欢鍒囧埌 `MAINTAIN + LOCKED`锛屽弽瀹℃椂鎭㈠ `ON_WARE + UN_LOCK`銆?- 缁翠慨鍑哄簱鍗?-> 閽卞寘锛氬鏍?鍙嶅鏃舵寜閲戦噸鍜屽伐璐瑰悓鏃舵墸鍑忔垨鍥炶ˉ渚涘簲鍟嗘潗璐ㄩ挶鍖呬笌鐜伴噾閽卞寘銆?

## 缁翠慨鍗曡鎯呰ˉ鍏呭洖鍐?- 缁翠慨鍗曡鎯?-> 缁翠慨鍗曟槑缁嗭細`finalization(...)` 浼氭妸閫変腑鏄庣粏缁熶竴鏀规垚 `replyStatus = COMPLETED`锛屽苟鍥炲啓 `finalizationTime/finalizationReason/finalizationMan`銆?- 缁翠慨鍗曡鎯?-> 缁翠慨鍗曟槑缁嗙疮璁★細缁堢粨鏃舵妸 `outNum/inNum` 鐩存帴琛ュ埌鑷冲皯绛変簬 `num`锛屼笉鏄彧鍋氱姸鎬佹爣璁般€?- 缁翠慨鍗曡鎯?-> 鍗曚欢琛細鑻ョ淮淇槑缁嗗甫 `itemId`锛岀粓缁撲細缁熶竴鍥炲啓 `wh_item.maintainBillDetailReplyStatus = COMPLETED`銆?- 缁翠慨鍗曡鎯?-> 缁翠慨鍗曞ご锛氬彧鏈夊悓涓€缁翠慨鍗曚笅鍏ㄩ儴鏄庣粏閮藉凡 `COMPLETED`锛屾墠浼氭妸 `wh_maintain_bill.replyStatus` 涓€璧锋帹杩涘埌瀹屾垚銆?- 缁翠慨鍗曡鎯?-> 鍗曚欢鍏ュ簱鍗?/ 鍗曚欢璋冩暣鍗曪細`createItemEntryDetailVOByIds/createItemAdjustDetailVOByIds` 浼氭妸缁翠慨鏄庣粏杩炲悓鈥滄渶杩戜竴娆＄淮淇叆搴撶粨鏋溾€濅竴璧蜂笅鎺ㄤ负鍚庣画鑽夌鏉ユ簮銆?

## 缁翠慨鍗曟姤琛ㄨˉ鍏呭洖鍐?- 缁翠慨鍗曟姤琛?-> 缁翠慨鍑哄簱鍗?/ 缁翠慨鍏ュ簱鍗曪細鎸夌粺涓€缁村害鑱氬悎閫佸巶涓庡洖鍘備袱渚?`num/goldWeight/grossWeight/cmtFee`锛屽舰鎴愬鐓х粺璁★紝涓嶇洿鎺ユ敼鍗曟嵁銆?- 缁翠慨鍗曟姤琛?-> 缁翠慨鍗?/ 缁翠慨鏄庣粏锛氭槑缁嗘姤琛ㄤ互 `wh_maintain_bill_detail` 涓轰富閿紝涓茶仈缁翠慨鍗曞ご銆佺淮淇嚭搴撱€佺淮淇叆搴撳拰缁堢粨瀛楁锛屽舰鎴愮敓鍛藉懆鏈熻鍥俱€?- 缁翠慨鍗曟姤琛?-> 缁堢粨灞曠ず锛氭姤琛ㄥ眰浼氭牴鎹?`finalizationReason` 鎶婂睍绀虹姸鎬佽鐩栨垚鈥滅粓缁撯€濓紝涓嶆槸鐩存帴鐓ф妱鍘熷 `replyStatus`銆?- 缁翠慨鍗曟姤琛?-> 缁翠慨鐘舵€佺瓫閫夛細`replyType` 閫氳繃 `out_num/in_num/step_ware_id` 鎺ㄥ鈥滃凡鍥?鏈洖/鍦ㄥ巶鈥濓紝灞炰簬鎶ヨ〃鍙ｅ緞浜屾璁＄畻銆?

## 濮斿鍔犲伐璁㈠崟琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 濮斿鍔犲伐璁㈠崟寤哄崟/鏀瑰崟 | `outsourceDemandOrder(detailList, true)` | 闇€姹傚崟鏄庣粏 | `producingNum`銆乣replyDate`銆乣overdueDays` | 濮斿浠舵暟涓庡洖澶嶄氦鏈熺洿鎺ュ崰鐢ㄩ渶姹傚崟绱 |
| 濮斿鍔犲伐璁㈠崟浣滃簾/鏀瑰崟鍥炴粴 | `outsourceDemandOrder(detailList, false)` | 闇€姹傚崟鏄庣粏 | 鍥為€€ `producingNum/replyDate` 褰卞搷 | 鏃ф槑缁嗗厛閲婃斁鍐嶉噸鍐?|
| 濮斿鍔犲伐璁㈠崟缁堟鏍搁攢 | `finishOutsourcing(...)` | 闇€姹傚崟鏄庣粏 | `cancelNum`銆乣cancelDate`銆乣overdueDays` | 鏈牳閿€閲忚浆鍏ラ渶姹傚崟鍙栨秷閲?|
| 璁㈠崟鏍搁攢 | 瀹℃牳/鍙嶅 | 濮斿鍔犲伐璁㈠崟鏄庣粏 | `totalWriteoffNum`銆乣totalPassNum`銆乣totalBadNum`銆乣totalReturnNum`銆乣writeoffDate` | 濮斿鍗曟牳閿€杩涘害鐢辨牳閿€閾惧弽鍚戦┍鍔?|
| 濮斿鍔犲伐璁㈠崟 | `waitEntryPage` | 鍗曚欢鍏ュ簱鍗?| 寰呭叆搴撴潵婧愭睜 | 浠モ€滄牳閿€閲?- 宸茬敓鎴愬崟浠跺叆搴撻噺鈥濅綔涓哄墿浣欏彲鍏ュ簱閲?|
| 濮斿鍔犲伐璁㈠崟鏄庣粏 | `inboundOptions` | 鎴愬搧鍏ュ簱鍗?| 杞垚鍝佸叆搴撴潵婧愭睜 | 鍙紑鏀?`DELIVERED` 涓?`convertInboundNo` 涓虹┖鐨勬槑缁?|

## 缁翠慨缁撶畻鏄庣粏琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 缁翠慨缁撶畻鏄庣粏 | `settleWeight` 鎸?`materialNo + settleClass` 鑱氬悎 | 瀹㈡埛閽卞寘 | 鏉愯川閽卞寘浣欓鍙樺姩 | 缁翠慨纭閾炬妸缁撶畻琛屾姌鎴愭潗璐ㄩ挶鍖呰緭鍏?|
| 缁翠慨缁撶畻鏄庣粏 | `settleMoney` 姹囨€?| 瀹㈡埛閽卞寘 | 鐜伴噾閽卞寘浣欓鍙樺姩 | 缁翠慨纭閾鹃澶栨嫾涓€鏉＄幇閲戦挶鍖呮暟鎹?|
| 缁翠慨缁撶畻鏄庣粏 | `settleNo = CLOSING_PRICE` 涓?`materialNo` 闈炵┖ | 缁撲环鍐插洖閽卞寘 | 缁撲环鍐插洖鏉愯川浣欓 | 缁翠慨纭閾惧崟鐙繃婊ょ粨浠疯 |
| 缁翠慨鍗?| `billId/billNo` | 缁翠慨缁撶畻鏄庣粏 | 褰掑睘瀛楁 | 缁撶畻鏄庣粏鐢熷懡鍛ㄦ湡渚濋檮缁翠慨鍗曪紝涓嶇嫭绔嬫祦杞?|

## 濮斿鍔犲伐璁㈠崟鏄庣粏琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 璁㈠崟鏍搁攢鏄庣粏 | `pass/bad/writeoff/return` | 濮斿鏄庣粏鏌ヨ | 鏉ヨ揣璁板綍椤?| `selectIncomingRecords(...)` 鐩存帴鎶婃牳閿€鏄庣粏鏄犲皠涓哄澶栨潵璐ц建杩?|
| 鍗曚欢鍏ュ簱鏄庣粏 | 宸插叆搴撴暟閲?閲戦噸 | 濮斿鏄庣粏寰呭叆搴撴睜 | `can_entry_num/can_entry_gram_weight` | 濮斿鏄庣粏椤垫寜鍓╀綑棰濆害寮€鏀惧崟浠跺叆搴撴潵婧?|
| 濮斿鏄庣粏 | `convertInboundNo` | 杞垚鍝佸叆搴撴潵婧愭睜 | 杩囨护宸插崰鐢ㄦ槑缁?| 闈炵┖鍗充粠 `inboundOptions(...)` 鎺掗櫎 |
| 濮斿鏄庣粏绱瀛楁 | `totalWriteoff/finish` | 閲囪喘姹囨€?| 鏈洖閲?鏍搁攢閲忕粺璁?| 姹囨€绘姤琛ㄦ寜绱瀛楁琛嶇敓鎵ц鎬佹寚鏍?|

## 缁撶畻鏂瑰紡琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 缁撶畻鏂瑰紡涓绘暟鎹?| `settleNo/bizType` | 鍚勪笟鍔＄粨绠楁槑缁?| 缁撶畻缂栫爜鍚堟硶鎬т笌鍚箟 | 閿€鍞€侀€€璐с€佹潵鏂欍€佺淮淇粨绠楅兘渚濊禆杩欏眰瀛楀吀 |
| 缁撶畻鏂瑰紡涓绘暟鎹?| `RECIRCULATION/CLOSING_PRICE` | 涓氬姟瑙勫垯鏍￠獙 | 杞瑺/缁撲环鍒嗘敮鏄惁鍏佽 | `checkSettleType(...)` 鐩存帴纭緷璧栦袱涓紪鐮?|

## 浠撳簱缁撴瀯琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 浠撳簱寤烘。 | 鑷姩鍒涘缓榛樿搴撳尯 `A` | 搴撳尯妗ｆ | 鍒濆搴撳尯鏁版嵁 | 寤轰粨涓嶆槸鍗曡〃鎻掑叆锛岃€屾槸浠撳偍缁撴瀯鍒濆鍖?|
| 搴撳尯寤烘。 | 鑷姩鍒涘缓榛樿搴撲綅 `A0` | 搴撲綅妗ｆ | 鍒濆搴撲綅鏁版嵁 | 寤哄簱鍖烘椂鍚屾鍒濆鍖栭涓簱浣?|
| 搴撲綅鍏变韩寮€鍏?| `stockShare` | 鍏变韩搴撲綅鍏崇郴 | `share_party_local` 鍒犳敼 | 鍏抽棴鍏变韩鏃朵細娓呯悊鍏变韩鍏崇郴 |
| 榛樿搴撲綅鑾峰彇 | `getDefaultLocal(...)` | 鍚勪笟鍔″崟鎹?| 榛樿 `localId` 鏉ユ簮 | 缁翠慨銆佸崟浠躲€佸簱瀛樼瓑閾句細渚濊禆杩欎釜鍏滃簳鍏ュ彛 |

## 瀹㈡埛妗ｆ琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 瀹㈡埛妗ｆ瀹℃壒閫氳繃 | `updateProcessResult(...)` | 璐㈠姟璐︽埛 | 婵€娲?鏇存柊 `wh_account` | 椤剁骇瀹㈡埛瀹℃壒閫氳繃鍚庢墠鐪熸鎶婅处鎴锋縺娲诲苟鏇存柊鍚嶇О |
| 瀹㈡埛妗ｆ瀹℃壒閫氳繃 | 鍚嶇О鍙樻洿 | 閽卞寘 | 鍚屾閽卞寘鍚嶇О | 瀹㈡埛鍚嶇О鍙樺寲鏃惰繕浼氬悓姝?`wh_wallet` 鍚嶇О |
| 瀹㈡埛妗ｆ瀹℃壒閫氳繃/鎵归噺瀹℃壒閫氳繃 | `handleDemandOrder(...)` | 闇€姹傚崟 | `customerName` | 闇€姹傚崟鍐椾綑瀹㈡埛鍚嶄細琚悓姝ユ洿鏂?|
| 瀹㈡埛妗ｆ鎻愬 | `biz_cache` 鏆傚瓨寰呭鎵圭増鏈?| 瀹㈡埛瀹℃壒閾?| 寰呯敓鏁堜笟鍔℃暟鎹?| 宸插鎵瑰鎴蜂慨鏀瑰悗锛屾柊鐗堟湰涓昏瀛樹簬缂撳瓨鑰岄潪绔嬪嵆瑕嗙洊涓昏〃 |
| 瀛愬鎴峰鍏?| `copyByParent(...)` | 瀛愬鎴蜂富妗?| 澶嶅埗涓氬姟鍖?涓氬姟鍛?鍖哄煙缂栫爜/瀹㈡埛绫诲瀷 | 瀛愬鎴疯蛋缁ф壙寮忓缓妗ｏ紝涓嶅崟鐙鎵?|

## 渚涘簲鍟嗘。妗堣ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 渚涘簲鍟嗗缓妗?瀵煎叆 | 缁戝畾鎴栬嚜鍔ㄥ垱寤鸿处鎴?| 璐㈠姟璐︽埛 | 鏂板缓 `wh_account` | 渚涘簲鍟嗚处鎴蜂粠寤烘。闃舵灏变竴璧风敓鎴?|
| 渚涘簲鍟嗘洿鏂?| 鍚嶇О/璐︽埛缂栫爜/璐︽埛绫诲瀷鍙樺寲 | 璐㈠姟璐︽埛 | 鏇存柊璐︽埛鍚嶇О銆佺紪鐮併€佺被鍨?| 渚涘簲鍟嗕笌璐︽埛鏄竴鏉″悓姝ュ彉鏇撮摼 |
| 渚涘簲鍟嗗垹闄?| 鍒犻櫎鍓嶆牎楠?| 閽卞寘 | 妫€鏌?`OPEN` 閽卞寘鏄惁瀛樺湪 | 寮€鏀鹃挶鍖呬細闃绘柇渚涘簲鍟嗗垹闄?|
| 渚涘簲鍟嗗彉鏇?| 瀛楁宸紓 | 鍙樻洿鏃ュ織 | `change_log` | 鏇存柊渚涘簲鍟嗕細鍐欎緵搴斿晢鍙樻洿璁板綍 |

## 鏉愯川妗ｆ琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鏉愯川妗ｆ鏇存柊 | `updateTexture(...)` | SKU | 鏉愯川缂栧彿/鏉愯川鍚嶇О鍐椾綑瀛楁 | 鏉愯川涓绘。淇敼浼氬悓姝ュ洖鍐?`wh_sku` |
| 鏉愯川妗ｆ | `walletTypeNo/walletGoodsType` | 閽卞寘鍙ｅ緞 | 鏉愯川閽卞寘绫诲瀷鏄犲皠 | 鏉愯川涓嶆槸绾睍绀哄瓧娈碉紝鑰屾槸閽卞寘鍒嗙被鏉ユ簮 |
| 鏉愯川妗ｆ | `semisClassNo/semisClassName/bigClassId` | 鍗婃垚鍝佸垎绫婚摼 | 鍒嗙被褰掑睘璇箟 | 鏉愯川鍚屾椂鍙備笌鍗婃垚鍝佸垎绫荤瓫閫?|

## 澶х被妗ｆ琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 澶х被妗ｆ鏇存柊 | `updateBigClass(...)` | SKU | 澶х被缂栧彿/澶х被鍚嶇О/`bigType` 鍐椾綑瀛楁 | 澶х被涓绘。淇敼浼氬悓姝ュ洖鍐?`wh_sku` |
| 澶х被妗ｆ | `salePriceMode/storePriceMode` | 鍟嗗搧瑙勫垯鍙ｅ緞 | 閿€鍞?瀛樿揣璁′环璇箟 | 澶х被鎵挎帴鍟嗗搧璁′环瑙勫垯瀛楁 |
| 澶х被妗ｆ | `goodsType/goodsClassify/bigType` | 鍟嗗搧褰掔被鍙ｅ緞 | 鍒嗙被璇箟 | 澶х被鏄晢鍝佺粨鏋勫眰鐨勯噸瑕佸垎绫昏妭鐐?|

## 鍝佺被缁撴瀯琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鍝佺被妗ｆ鏇存柊 | `updateCategory(...)` | SKU | 鍝佺被缂栧彿/鍝佺被鍚嶇О鍐椾綑瀛楁 | 涓€绾у搧绫诲彉鏇翠細鍚屾鍥炲啓 `wh_sku` |
| 瀛愬搧绫绘洿鏂?| `updateSubCategoryByName(...)` | SKU | 瀛愬搧绫荤紪鍙峰啑浣欏瓧娈?| 瀛愬搧绫诲彉鏇存寜鈥滅埗鍝佺被 + 瀛愬搧绫诲悕绉扳€濆洖鍐?SKU |
| 鍝佺被/瀛愬搧绫诲缓妗ｆ垨鏀规。 | `CategoryBusiness` | 鍟嗗煄鍒嗙被鏍?| `product_category` 鑺傜偣 | WMS 鍝佺被缁撴瀯浼氬悓姝ュ埌鍟嗗煄鍒嗙被鏍?|
| 鍝佺被璇︽儏鑱氬悎 | `getSimpleListDetail(...)` | 鍟嗗搧缁撴瀯瑙嗗浘 | 瀛愬搧绫?瑙勬牸/鍥涚骇/浜旂骇/浜у搧璧勬枡 | 涓€绾у搧绫绘槸鍟嗗搧缁撴瀯灞傜殑鑱氬悎瀹夸富 |

## 浜у搧璧勬枡琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 浜у搧璧勬枡鏌ヨ | `fillData(...)` | 灞曠ず瑙嗗浘 | `categoryName/textureName` | 浜у搧鏈綋鍙瓨 ID锛屽睍绀哄眰杩愯鏃惰ˉ榻愬悕绉?|
| 浜у搧璧勬枡瀵煎叆 | `categoryId`(瀹為檯涓哄搧绫荤紪鍙? | 鍝佺被涓绘。 | 瑙ｆ瀽鐪熷疄 `categoryId` | 瀵煎叆閾句細鍏堟寜鍝佺被缂栧彿鍖归厤鍝佺被 |
| 浜у搧璧勬枡瀵煎叆 | `productType` 鏂囨 | 瀛楀吀 | `product_type` 鍊?| 瀵煎叆鏃朵細鎶婁骇鍝佺被鍨嬫枃妗堣浆鎴愮郴缁熷瓧鍏稿€?|
| 浜у搧璧勬枡绠€鍗曞垪琛?| `getProductListByCategoryId(...)` | 涓氬姟閫夋嫨鏉ユ簮姹?| 鍝佺被绛涢€夊晢鍝佸垪琛?| 浜у搧璧勬枡鏄緢澶氫笟鍔＄晫闈㈢殑鍟嗗搧鏉ユ簮姹?|

## 娆惧紡搴撹ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 娆惧紡寤烘。 | 榛樿渚涘簲鍟?BOM | SKU涓绘。 | 榛樿渚涘簲鍟嗐€佸伐璐广€佸叾浠栬垂鐢ㄧ瓑瀛楁 | 榛樿渚涘簲鍟嗚垂鐢ㄤ細鏁翠綋鎶曞奖鍥?`wh_sku` |
| 娆惧紡瀹℃壒 | `biz_cache` 寰呭鎵圭増鏈?| SKU涓绘。 | 瀹℃壒閫氳繃鍚庤鐩栦富琛?| SKU 鐢熸晥鐗堟湰璧扮紦瀛樺鎵归摼 |
| 娆惧紡璇︽儏 | 瑙勬牸/BOM/杈呯煶/閰嶄欢鏌ヨ | 鑱氬悎璇︽儏瑙嗗浘 | `SkuRespVO` | 娆惧紡璇︽儏涓嶆槸鍗曡〃璇︽儏锛岃€屾槸澶氳〃鑱氬悎 |
| 娆惧紡寤烘。/鏀规。 | `CategoryBusiness` 绫讳技妯″紡 | 搴撳瓨/鍗曚欢/閿€鍞瓑鎵ц閾?| 鍙墽琛屽晢鍝佸璞℃潵婧?| SKU 鏄涓笟鍔℃ā鍧楀叡鍚屾秷璐圭殑鍟嗗搧鏍稿績瀹炰綋 |

## 渚涘簲鍟嗘寮?/ BOM 琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 渚涘簲鍟嗘寮?/ BOM | `isDirect = 鏄痐 榛樿渚涘簲鍟嗚 | SKU涓绘。 | 榛樿鎴愭湰/閿€鍞垂鐢ㄥ瓧娈垫姇褰?| `sku` 寤烘鍜屾敼娆炬椂浼氭妸榛樿渚涘簲鍟?BOM 璐圭敤澶嶅埗鍥?`wh_sku` |
| 渚涘簲鍟嗘寮?/ BOM | `getBomBySkuNos(...)` 宸﹁繛 accessories + part | SKU璇︽儏/瀵煎嚭瑙嗗浘 | 鑱氬悎 BOM 缁撴瀯 | SKU 璇︽儏涓嶆槸鍙湅澶磋〃锛岃€屾槸鏁村 BOM 鑱氬悎缁撴灉 |
| 渚涘簲鍟嗘寮?/ BOM | `bomSerialNo` | 楗扮煶/杈呮枡鏌ヨ閾?| accessories 鍒嗙粍閿氱偣 | `getAccessoriesBySerialNos(...)` 浼氱敤 `bomSerialNo` 鍙嶆煡骞堕噸鍒嗙粍 |
| 渚涘簲鍟嗘寮?/ BOM | 鍒犻櫎澶磋〃/鍗槦琛?| SKU涓绘。 / 鍘嗗彶鏌ヨ閾?| 榛樿璐圭敤鏉ユ簮涓?BOM 鏄庣粏娑堝け | 褰撳墠鍒犻櫎閮芥槸鐗╃悊鍒犻櫎锛屽奖鍝嶈寖鍥翠笉姝㈠崟琛?|

## SKU 瑙勬牸琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鏂板缓娆剧紦瀛樿鏍?| `wh_new_sku_specs` | SKU瀹℃壒鑱氬悎瑙嗗浘 | 鏂板缓娆捐鏍艰緭鍏?| 鏂板缓娆惧鎵规湡闂磋鍙栫殑鏄紦瀛樿鏍硷紝涓嶆槸姝ｅ紡瑙勬牸琛?|
| 鏀规缂撳瓨瑙勬牸 | `wh_update_sku_specs` | SKU瀹℃壒鑱氬悎瑙嗗浘 | 鏀规瑙勬牸杈撳叆 | 鏀规瀹℃壒鏈熼棿鐨勮鏍间慨鏀瑰厛鍋滅暀鍦ㄧ紦瀛樿〃 |
| 姝ｅ紡瑙勬牸琛?| `wh_sku_specs` | SKU璇︽儏/瀵煎嚭/鏌ヨ | 鐢熸晥瑙勬牸灞曠ず | 瀹℃壒鐢熸晥鍚庤鎯呴〉鍜屽鍑洪〉璇诲彇鐨勬槸姝ｅ紡瑙勬牸 |
| SKU瑙勬牸鍒犻櫎 | `deleteSkuSpecsBySkuNo(skuNo)` | SKU瑙勬牸灞?| 鎸夋鍙锋暣缁勬竻鐞?| 褰撳墠鍒犻櫎鏄墿鐞嗗垹锛屽奖鍝嶆暣缁勮鏍煎彲瑙佹€?|

## SKU 瀹℃壒鍩熻ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鏂板缓娆剧紦瀛樺煙 | `wh_new_sku_detail/specs/supplier...` | 姝ｅ紡 SKU 鍩?| 涓绘。/瑙勬牸/BOM 鐢熸晥杈撳叆 | 鏂板缓娆惧鎵归€氳繃鍓嶏紝姝ｅ紡 `sku` 鍩熻鍙栦笉鍒拌繖浜涚紦瀛樻暟鎹?|
| 鏀规缂撳瓨鍩?| `wh_update_sku_detail/specs/supplier...` | 姝ｅ紡 SKU 鍩?| 宸紓鍚堝苟杈撳叆 | 鏀规瀹℃壒鐢熸晥鍓嶏紝鍙樻洿鍐呭鍏堝仠鐣欏湪缂撳瓨鍩?|
| 鏂板缓娆?鏀规瀹℃壒 | `submit/approved/reject/transfer` | 浼佸井寰呭姙 / 鎶勯€?/ 娑堟伅鎻愰啋 | 寰呭姙鍒楄〃涓庡崗鍚岄€氱煡 | SKU 瀹℃壒鍩熸妸瀹℃壒鍔ㄤ綔鐩存帴鑱斿埌浼佸井鍜岄€氱煡閾?|
| 鏀规闃舵妯℃澘 | `state -> UpdateSkuStageEnum` | 瀵煎叆瀵煎嚭閾?| 鍔ㄦ€佹ā鏉夸笌闃舵瀵煎叆 | 鏀规瀵煎叆妯℃澘涓嶆槸鍥哄畾妯℃澘锛岃€屾槸鎸夐樁娈靛垏鎹?|

## SKU 瀹℃壒鏄庣粏瀹夸富琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鏂板缓娆炬槑缁?| `state/latestOperateBatch/processUserId` | 鍒楄〃椤?/ 浼佸井寰呭姙 | 鎵规鑱氬悎涓庨樁娈靛睍绀?| 鏂板缓娆惧緟鍔炰笉鏄洿鎺ョ湅鏃ュ織锛岃€屾槸鍏堣鏄庣粏瀹夸富 |
| 鏂板缓娆炬棩蹇?| `operateBatch/operateType/operateRemark` | 宸插/宸查┏鍥炶鍥?| 瀹℃壒杞ㄨ抗鍥炴斁 | 鍘嗗彶瀹℃壒瑙嗗浘渚濊禆鏃ュ織鍐嶅弽鏌ユ槑缁?|
| 鏀规鏄庣粏 | `state/startState` | 鏀规鍒楄〃 / 绫诲瀷绛涢€?/ 鏈悓姝ユ睜 | 闃舵涓庣被鍨嬭В閲?| 鏀规绫诲瀷鏈€缁堟槧灏勫埌 `startState` 杩囨护 |
| 鏀规鏃ュ織 | `operateBatch/operateType` | 鏀规鍘嗗彶杞ㄨ抗 | 瀹℃壒/椹冲洖杞ㄨ抗 | 鎵规瀹℃壒鍘嗗彶闈犳棩蹇椾簨浠惰〃琛ラ綈 |

## SKU 瀹℃壒鍩?BOM 缂撳瓨琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鏂板缓娆?supplier 缂撳瓨 | `isDirect = TRUE` | 鏂板缓娆惧鎵硅鎯?/ 姝ｅ紡 BOM 鐢熸晥閾?| 榛樿渚涘簲鍟嗚瘑鍒?| 榛樿渚涘簲鍟嗗湪缂撳瓨鏈熷氨宸茬‘瀹?|
| 鏀规 supplier 缂撳瓨 | `listBySkuNo/listBySkuNos` | 鏀规宸紓鏌ヨ | 鎸夋寮忔鍙峰弽鏌ョ紦瀛?BOM | 鏀规缂撳瓨鏃㈡寕鏄庣粏涔熸寕姝ｅ紡娆惧彿 |
| 瀹℃壒鍩?supplier 缂撳瓨 | 璐圭敤瀛楁闆嗗悎 | SKU涓绘。 / 姝ｅ紡 BOM | 榛樿璐圭敤涓?BOM 鐢熸晥杈撳叆 | 瀹℃壒閫氳繃鍓嶇殑璐圭敤婧愬ご灏卞湪缂撳瓨 BOM |
| 瀹℃壒鍩?accessories / part 缂撳瓨 | `skuSupplierId` | BOM 璇︽儏鑱氬悎 | 杈呯煶/閰嶄欢缁撴瀯 | 鍗槦琛ㄩ€氳繃 supplier 缂撳瓨澶磋仛鍚堜负鏁村 BOM |

## SKU 瀹℃壒鍩熻緟鍔╅摼琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| Excel 瀵煎叆鍒嗘瀽 BO | 涓绘暟鎹槧灏?+ 寰呰惤缂撳瓨瀵硅薄 | newsku / updatesku 缂撳瓨鍩?| 鎵归噺瑁呴厤钀借〃 | 瀵煎叆鍏堣蛋 BO 鍒嗘瀽锛屽啀缁熶竴鍐欑紦瀛樺煙 |
| 鏂板缓娆惧悓姝?| `synchronize(...)` | 姝ｅ紡 SKU 鍩?| 涓绘。/瑙勬牸/BOM 姝ｅ紡鐢熸晥 | 鏂板缓娆惧悓姝ユ槸鐪熷疄钀芥寮忚〃鍔ㄤ綔 |
| 鏀规鍚屾 | `syncNormal / syncDesign / syncDown / syncShelves` | 姝ｅ紡 SKU 鍩?| 鎸夋敼娆剧被鍨嬭鐩栨寮忓瓧娈?| 鏀规涓嶆槸缁熶竴瑕嗙洊锛岃€屾槸鍒嗘敮鍚屾 |
| 鏀规鍚屾 | 娓?`biz_cache` + Redis `sku::id=*` | 鍟嗗搧缂撳瓨閾?| 姝ｅ紡璇诲彇缂撳瓨澶辨晥 | 鍚屾鍚庣珛鍗虫竻姝ｅ紡鍟嗗搧缂撳瓨 |

## SKU 瑙勫垯涓绘暟鎹ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 璁捐鍏冪礌涓绘暟鎹?| `designNo/designName/elementBigType/elementSubType` | newsku / updatesku / SKU | 璁捐鍏冪礌鏍￠獙涓庡悕绉版槧灏?| 瀹℃壒鍩熷拰璇︽儏椤甸兘渚濊禆瀹冭В閲婅璁″厓绱犲瓧娈?|
| 璁捐鍏冪礌涓绘暟鎹?| `subTypeOptions(...)` | 鍓嶇绛涢€?/ 閫夐」婧?| 瀛愮被閫夐」闆嗗悎 | 瀛愮被鏉ユ簮鍚屾椂渚濊禆鍝佺被鍜屼笁濂楀瓧鍏?|
| 鐢熶骇宸ヨ壓涓绘暟鎹?| `processNo/processName` | newsku / updatesku / SKU | 宸ヨ壓鏍￠獙涓庡悕绉版牸寮忓寲 | 鎵归噺璁捐鍏冪礌淇敼浼氭妸宸ヨ壓缂栧彿杞垚宸ヨ壓鍚嶇О |
| 鐢熶骇宸ヨ壓涓绘暟鎹?| `importExcel(...)` | 鐢熶骇宸ヨ壓涓绘。 | 鑷姩琛ョ紪鐮佸苟鍚敤 | 瀵煎叆閾句細鐩存帴褰卞搷鍚庣画 SKU 宸ヨ壓閫夐」姹?|

## 绯诲垪涓庡绯绘槧灏勮ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 绯诲垪涓绘。 | `seriesNo/seriesName` | 绯诲垪鏄犲皠 / SKU 鏌ヨ閾?| 绯诲垪缂栧彿鍒板悕绉版槧灏?| 涓昏渚涘叧绯绘煡璇㈠拰灞曠ず瑙ｉ噴 |
| 绯诲垪鏄犲皠 | `seriesId + skuIds` | `wh_sku_series_mapping` | 鎵归噺鍏崇郴钀借〃 | 涓€涓郴鍒楀彲鎵归噺鎸傚娆?SKU |
| 濂楃郴鏄犲皠 | 鍒涘缓/鏇存柊濂楃郴 | `wh_sku` | 鍥炲啓 `setNo/setName` | 濂楃郴涓嶆槸鍗曠函鍏崇郴琛紝浼氱洿鎺ユ敼 SKU 涓绘。 |
| 濂楃郴鏄犲皠 | 鏇存柊 SKU 濂楃郴褰掑睘 | 鍟嗗搧缂撳瓨閾?| 娓?BizCache / Redis | 濂楃郴鍙樻洿鍚庣珛鍗虫竻鐞嗗晢鍝佺紦瀛?|

## SKU 缂栫爜瑙勫垯琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| SKU 缂栫爜瑙勫垯 | `categoryCode + supplierCode + saleFeeCode` | `wh_sku_rule` | 瀹氫綅搴忓垪瑙勫垯琛?| SKU 缂栫爜涓嶆槸鍏ㄥ眬娴佹按锛岃€屾槸涓夊厓缁勫眬閮ㄥ簭鍒?|
| `genSkuRule(...)` | 鐢熸垚涓嬩竴娴佹按鍙?| 姝ｅ紡 SKU 钀藉彿閾?| 涓嬩竴涓?`serialNo` | 鐪熸姝ｅ紡钀藉彿鍓嶅厛浠庤鍒欏涓诲彇涓嬩竴鍙?|
| 姝ｅ紡 SKU 钀藉彿 | `skuId/skuNo/skuName/serialNo` | `wh_sku_rule_log` | 缂栫爜杞ㄨ抗璁板綍 | 缂栫爜鏈€缁堢粰浜嗚皝瑕侀潬鏃ュ織杩芥函 |
| 瑙勫垯鏃ュ織 | `saleFeeCode/basicGramCmtFee/moreGramCmtFee` | 鍘嗗彶杩芥函閾?| 褰撴椂缂栫爜鍙ｅ緞 | 鏃ュ織鎶婄紪鐮佺敓鎴愭椂鐨勮垂鐢ㄥ彛寰勪竴璧峰浐鍖?|

## 鎶勯€侀厤缃ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鎶勯€侀厤缃?| `menuId + ccRecipientIds` | newsku / updatesku 浼佸井椤?| 鎶勯€佷汉鍙鐘舵€侀泦鍚?| 瀹℃壒鍩熶細鎸夎彍鍗曢厤缃瘑鍒摢浜涙妱閫佷汉鑳界湅鍝簺鐘舵€?|
| 鎶勯€侀厤缃?| `menuId + transferIds` | newsku / updatesku 杞姙鍏ュ彛 | 杞姙鍊欓€変汉鍒楄〃 | 杞姙浜轰笉鏄啓姝伙紝鑰屾槸閰嶇疆椹卞姩 |
| 鎶勯€侀厤缃?| 鍚敤鐘舵€佷笅鏈€鏂颁竴鏉?| 涓氬姟璇诲彇閾?| 瀹為檯鐢熸晥瑙勫垯 | 鍚?menuId 澶氭潯鍚敤鏃堕粯璁ゅ彇鏈€鏂拌褰?|

## SKU 渚涘簲鍟嗗簱琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 姝ｅ紡 SKU 涓绘。 | `productionProcess/factorySkuNo/surfaceTechnics` | `wh_sku_supplier` | 鍐椾綑鎽樿瀛楁 | 寤轰緵搴斿晢褰掑睘鍏崇郴鏃朵細浠庢寮?SKU 澶嶅埗杩囨潵 |
| 渚涘簲鍟嗕富妗?| `supplierId` | `wh_sku_supplier` | 渚涘簲鍟嗗綊灞為敭 | 鏋勬垚 SKU 涓庝緵搴斿晢鐨勮交閲忓叧绯荤储寮?|
| SKU 鏂囦欢閾?| `FILE_BIZ_TYPE_SKU` 鍥剧墖 | 渚涘簲鍟嗘寮忓簱瑙嗗浘 | 鍥剧墖灞曠ず | 鍥剧墖涓嶅瓨浜?`wh_sku_supplier`锛岃€屾槸杩愯鏃惰ˉ榻?|

## 瀹㈡埛渚?SKU 鍏崇郴鍩熻ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 姝ｅ紡 SKU 涓绘。 | `skuId/skuNo/skuName` | `wh_customer_sku` | 瀹㈡埛娆炬寕鎺ヤ笌瑙嗗浘琛ラ綈 | `customersku` 寤烘。鏃跺浐鍖?`skuId/skuNo`锛屾煡璇㈡椂鍙堢户缁€熸寮?SKU 琛ヨ鍥?|
| 瀹㈡埛涓绘。 | `customerId/status` | `wh_customer_sku` | 瀹㈡埛娆惧瓨鍦ㄦ€т笌鍚仠鏍￠獙 | 寤烘。銆佸鍏ャ€佸垎椤垫潈闄愰兘浼氫緷璧栧鎴蜂富妗?|
| 閿€鍞崟缁熻 | `saleSkuCount(...)` | 瀹㈡埛娆惧垪琛ㄨ鍥?| 閿€鍞欢鏁扮瓫閫?鍥炴樉 | `customersku` 鏀寔鎸夐攢鍞欢鏁板尯闂寸瓫閫?|
| 姝ｅ紡渚涘簲鍟?BOM | `supplier/accessories` | 瀹㈡埛娆捐鎯呰鍥?| 閰嶄欢/BOM 琛ラ綈 | 鏈夋潈闄愭椂瀹㈡埛娆捐鎯呬細鐩存帴鍊熸寮?BOM 缁撴瀯灞曠ず |
| 姝ｅ紡 SKU 涓绘。 | `skuNo/skuName` | `wh_sku_customer` | 鏃х储寮曞鎴锋鍙?娆惧悕 | `skucustomer` 鍒涘缓鏃剁洿鎺ュ鍒舵寮?SKU 缂栫爜涓庡悕绉?|
| 姝ｅ紡 SKU 涓绘。 | 鍒嗙被/宸ヨ壓/閲嶉噺/澶囨敞 | `wh_sku_customer_lm` | 鑰佸簷瀛楁鎶曞奖 | `skucustomerlm` 鍒涘缓鏃舵妸姝ｅ紡 SKU 缈昏瘧鎴愯€佸簷娓犻亾瀛楁妯″瀷 |

## 娆惧紡妯″彿琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 姝ｅ紡 SKU | `skuId/skuNo/sampleGramWeight/bigTypeNo` | `wh_sku_model` | 妯″彿涓绘。鎶曞奖 | 鎵嬪伐寤烘。鍜岃嚜鍔ㄧ敓鎴愰兘浼氫粠姝ｅ紡 SKU 澶嶅埗杩欎簺瀛楁 |
| 閲嶉噺娈?| `segmentNo/minWeight/maxWeight` | `wh_sku_model` | 妯″彿閲嶉噺杈圭晫 | 妯″彿鏈川涓婃槸 SKU 涓庨噸閲忔鐨勭粦瀹氱粨鏋?|
| `genSkuModel*` | 鑷姩琛ユā鍙?| `wh_sku_model` | 鏂板鎴栨洿鏂版ā鍙?| 涓嶆槸鍙湁鍚庡彴 CRUD 浼氬啓杩欏紶琛?|
| 鑰佸簷鍏变韩寮€鍏?| `update-share-status-lm` | `wh_sku_model.shareStatusLm` | 鑰佸簷鍏变韩鐘舵€?| 妯″彿灞傜洿鎺ユ寕浜嗚€佸簷鍏变韩鏍囪 |

## 鍏变韩搴撳瓨琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| `wh_sku_stock` | 鍏变韩搴撲綅搴撳瓨 | `wh_share_stock` | `shareStock/surplusStock` | 寮€鍚叡浜椂鎸?`skuNo+modelNo+specs` 姹囨€昏繘涓昏〃 |
| 鍏变韩娴佹按 | `on/off/adjust` | `wh_share_flow_record` | 鍏变韩搴撳瓨鎿嶄綔娴佹按 | 鍏变韩寮€鍏炽€佽皟骞炽€佸悓姝ラ兘浼氳惤娴佹按 |
| 鑰佸簷鎺ュ彛 | `pushInOutOrder/batchPushInOutOrder` | `wh_lm_api_transfer` | 鎺ュ彛鐥曡抗 | 姣忔鎺ㄩ€侀兘浼氳褰曡姹傜粨鏋?|
| 宸紓璋冨钩 | `diffStock` | `wh_share_stock` | `surplusStock` 鍥炶ˉ | 璋冨钩鎴愬姛鍚庣敤 SQL 鎶婂樊寮傚洖琛ヨ繘缁撲綑搴撳瓨 |
| 鏄ㄦ棩缁撲綑涓婃姤 | 鏃ョ粨蹇収 | `wh_share_surplus_day_report` | 鏃ユ姤澶囦唤 | 鍏变韩搴撳瓨鏀寔鎸夋棩鐣欑棔澶囦唤 |

## 鍏变韩鏂硅ˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鍏变韩鏂逛富妗?| `id/code/name/customerId` | `wh_share_party_local` | 鍏变韩鏂瑰綊灞炲叧绯?| 鍏变韩搴撲綅鍏崇郴浠ュ叡浜柟涓绘。涓哄涓?|
| 鍏变韩鏂瑰簱浣嶈缃?| `setting(id, localIds)` | 鍏变韩搴撳瓨閾?| 搴撲綅澧為噺涓嬪彂/绉婚櫎 | 鍏变韩鏂瑰簱浣嶈皟鏁翠細缁х画瑙﹀彂鍏变韩搴撳瓨娑堟伅 |
| 鍏变韩鏂瑰簱浣?| `localId` | 鍏变韩鏂瑰簱瀛樿鍥?| 搴撲綅/浠撳簱/缃戠偣鑱旀煡 | 鍏变韩鏂瑰簱瀛橀〉鐩存帴渚濊禆搴撲綅鍏崇郴琛?|

## 鍏变韩娴佹按涓庤€佸簷鎺ュ彛鐥曡抗琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鍏变韩涓氬姟鍔ㄤ綔 | `bizType/billNo/skuNo/modelNo/specs/quantity` | `wh_share_flow_record` | 涓氬姟娴佹按钀借〃 | 鍏变韩寮€鍚€佸叧闂€佽皟骞炽€佺粨绠楀悓姝ラ兘浼氫骇鐢熸棩蹇楁祦姘?|
| 鍏变韩娴佹按 | `serialNumber` | `wh_lm_api_transfer` | 鎺ュ彛鐥曡抗涓茶仈閿?| 鍚屼竴鎵逛笟鍔℃祦姘翠笌鑰佸簷鎺ュ彛璋冪敤閫氳繃鎵规鍙风粦瀹?|
| 鑰佸簷鎺ュ彛鍥炴墽 | `resultCode/resultMsg/responseData` | `wh_share_flow_record` | 鐘舵€?澶辫触鍘熷洜鍥炲啓 | 鎺ュ彛缁撴灉鏈€缁堥┍鍔ㄥ叡浜祦姘存垚鍔熸垨澶辫触 |
| 鍥炶皟鐥曡抗 | `callback*` | `wh_lm_api_transfer` | 鍥炶皟闂幆瀛楁 | 鎺ュ彛琛ㄩ鐣欎簡瀹屾暣鍥炶皟澶勭悊鐥曡抗 |

## 鑰佸簷璁㈠崟涓庡洖璋冩帴鍙ｈˉ鍏呭洖鍐?| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鑰佸簷涓嬪崟璇锋眰 | `/order/create` | `customerOrderService` | 鍒涘缓鑰佸簷瀹㈡埛璁㈠崟 | 鎺у埗灞傚彧鍋氬崗璁牎楠屼笌杩斿洖灏佽 |
| 鑰佸簷璁㈠崟鐘舵€侀€氱煡 | `/order/stateChange` | `customerOrderService` | 鑰佸簷璁㈠崟鐘舵€佹帹杩?| 鐘舵€佸悓姝ョ湡瀹炶惤鍦ㄥ鎴疯鍗曞煙 |
| 鑰佸簷 PP 閫氱煡 | `/order/ppNotify` | `customerOrderService` | PP 鍗曠粨鏋滃洖鍐?| 鎺у埗灞傚彧鏄崗璁€傞厤鍏ュ彛 |
| 鑰佸簷妗ｆ鍥炶皟 | `/callback/gyswy/pushDplist` | `syncTaskService` | 鍚屾浠诲姟缁撴灉鍥炲啓 | 鍗曞搧妗ｆ鍚屾缁撴灉鍥炰紶璧板悓姝ヤ换鍔￠摼 |
| 鑰佸簷鍙戣揣/PP 鍥炶皟 | `/callback/pushYxysOrder` `/callback/pushPPOrder` | `deliveryBusiness` | 鍙戣揣缁撴灉娑堣垂 | 褰撳墠鐪熷疄澶勭悊閾惧凡缁忓亸鍚戜氦浠樹笟鍔″眰 |

## 鎺ュ彛鍚屾浠诲姟琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 鍚屾璇锋眰鍙戣捣 | `bizType/bizId/bizNo/request*` | `wh_sync_task` | 鍚屾浠诲姟钀借〃 | 鐪熸涓氬姟鍚屾浼氬厛钀戒换鍔″涓?|
| 鑰佸簷妗ｆ鍥炶皟 | `task_id/callback data` | `wh_sync_task` | `callback*` 瀛楁鍥炲啓 | 鍗曞搧妗ｆ鍚屾缁撴灉鐩存帴鍥炲啓浠诲姟琛?|
| 鍚屾浠诲姟鍥炶皟澶勭悊 | `callbackStatus/callbackResult` | 涓氬姟鍗曟嵁 | 鍗曟嵁鍚屾鐘舵€佹帹杩?| 閮ㄥ垎鍥炶皟浼氱户缁敼 `entryBill`銆乣saleBill`銆乣customerOrder` |
| 鑱氬悎鏌ヨ | `bizNo + bizType` | `CustomerOrderIntentBusiness` | 浠诲姟鍒楄〃鑱氬悎 | 鏌愪簺浠诲姟鏌ヨ涓嶇洿鎺ヨ琛紝鑰屾槸璧颁笟鍔¤仛鍚堣鍥?|

## 2026-03-31 鏃х増鍏变韩搴撳瓨鍩?- `stockshare`锛氭棫鐗堝叡浜簱瀛樺涓伙紝鎵挎帴璁㈠崟閿佸畾銆佸嚭鍏ュ簱鍚屾銆佸簱瀛樻牳瀵广€佸樊棰濆悓姝ャ€?- `itemshare`锛氬崟浠跺叡浜笌鍚屾缁撴灉鍥炲啓瀹夸富锛宍syncId/syncStatus` 鐩存帴鎸傚湪鍏变韩鍏崇郴涓娿€?- `stocksharelog`锛氭棫鐗堝叡浜簱瀛樹簨浠跺眰锛岃褰曞叡浜暟閲忓墠鍚庡€硷紝骞跺洖鎸傚悓姝ョ粨鏋溿€?- 浜ゅ弶鍐欏洖锛歚synctask` 鍥炲啓 `itemshare.syncStatus` 涓?`stocksharelog.syncStatus`锛涘鎴疯鍗曚細閿佸畾 `stockshare.numLock` 鍙ｅ緞銆?

## 2026-03-31 鍑鸿揣灞ョ害鍩?- `deliverybill`锛氶攢鍞粨绠椾笅娓稿饱绾﹀涓伙紝鎵挎帴鑰佸簷鍑鸿揣鎺ㄩ€併€佸洖璋冪粨鏋滃拰鍏变韩搴撳瓨寤惰繜鎵ｅ噺銆?- 浜ゅ弶鍐欏洖锛氳€佸簷鍥炶皟浼氬悓鏃舵敼 `deliverybill.status`銆乣customerorder.orderStatus/outOrderStatus/deliveryNotify`銆乣salebill.syncStatus`銆乣lmapitransfer.callback*`銆?- 寤惰繜閾撅細鍑鸿揣鎴愬姛鍚?`LmStockSyncDelayQueue` 鍐嶇敓鎴?`shareflowrecord` 骞剁疮璁″洖鍐?`sharestock`銆?

## 2026-03-31 瀹㈡埛璁㈠崟鍩?- `customerorder`锛氬鎴峰饱绾﹀煙瀹夸富锛岀粺涓€鎵挎帴鐜板満鍗曘€佸鐣欏瓨鍗曘€佸钩鍙板崟銆佸嚭璐у洖鍐欏拰鍚屾鐘舵€併€?- 浜ゅ弶鍐欏洖锛歚deliverybill` 鍥炲啓 `customerorder.orderStatus/outOrderStatus/deliveryNotify/outPpOrderNo`锛沗syncTask` 鍜?`lmapitransfer` 鍏卞悓鎵挎帴瀵瑰鍚屾鐥曡抗锛沗pack/salebill` 鎺ㄥ姩璁㈠崟浠庡緟鎷ｈ揣杩涘叆寮€鍗?鍙戣揣閾俱€?

## 2026-03-31 瀹㈡埛璁㈠崟鎺ュ崟灞?- `customerorderreceive`锛氬鎴疯鍗曞煙鍓嶇疆鍏ュ彛锛屾壙鎺ヤ笟鍔″憳寤哄崟銆佹帴鍗曘€佹嫆鍗曘€佽浆闇€姹備笌鍒拌揣纭銆?- 浜ゅ弶鍐欏洖锛歚taking(...)` 浼氳仈鍔ㄨ鍗曟秷鎭姸鎬侊紱`toDemandOrder(...)` 浼氭帹杩涜鍗曡繘鍏ュ緟鍥炶揣骞惰Е鍙戠敓浜ч€氱煡銆?

## 2026-03-31 瀹㈡埛璁㈠崟鐪嬫澘灞?- `customerorderkanban`锛氬鎴疯鍗曞煙 BI 鑱氬悎灞傦紝鎷兼帴璁㈠崟鏄庣粏璁″垝閲忎笌鍗曚欢鎵ц鍙戣揣閲忋€?- 浜ゅ弶璇诲彇锛歚customerorder.getKanbanCount/getKanbanTotal`銆乣customerorderdetail.getKanbanDetail`銆乣customerorderitem.getKanbanItem`銆?

## 2026-03-31 瀹㈡埛鍏变韩搴撲綅灞?- `customerlocation`锛氬叡浜簱瀛樹笂娓搁厤缃叆鍙ｏ紝缁存姢瀹㈡埛涓庡叡浜簱浣嶇粦瀹氥€?- 浜ゅ弶鍐欏洖锛歚shareAll/shareOne` 浼氱洿鎺ヨЕ鍙?`sharestock.onShareStock/offShareStock`銆?
## 2026-03-31 瀹㈡埛瀛楀嵃琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 瀹㈡埛妗ｆ | 寤烘。/鎵归噺澶勭悊 | wh_customer_marking | 榛樿瀛楀嵃琛ュ缓 | 瀹㈡埛寤烘。鍚庝細鑷姩琛ヤ竴鏉″叕鍏遍粯璁ゅ瓧鍗?|
| 瀹㈡埛瀛楀嵃 | markingId -> content | wh_customer_order | printContent | 灏忕▼搴?澶栭儴璁㈠崟鎻愪氦鏃舵妸瀛楀嵃鍐呭鍐欏叆鎵撳嵃鍐呭 |
| 瀹㈡埛瀛楀嵃 | markingId -> markingNo | wh_pack | markingNo | 浜у搧鍖呭缓鍗曚細鏍￠獙瀛楀嵃骞跺洖鍐欏瓧鍗扮紪鍙?|
| 瀹㈡埛瀛楀嵃 | markingNo | wh_sale_bill | 瀛楀嵃缂栧彿鎵挎帴 | 閿€鍞摼缁х画娌跨敤鍓嶅簭瀛楀嵃缂栧彿 |

## 2026-03-31 瀹㈤攣绠＄悊琛ュ厖鍥炲啓
| 鏉ユ簮妯″潡 | 鏉ユ簮瀛楁/鍔ㄤ綔 | 鐩爣妯″潡 | 鐩爣瀛楁/鍔ㄤ綔 | 璇存槑 |
|---------|---------------|---------|---------------|------|
| 瀹㈡埛璁㈠崟/鎸戣揣 | 瀹㈤攣鍗犵敤 | wh_item | customerLock 绛夐攣瀹氬彛寰?| 瀹㈤攣鐪熷疄瀹夸富鎸傚湪鍗曚欢涓昏〃 |
| 瀹㈤攣绠＄悊 | elease/batch-release | wh_item | 閲婃斁瀹㈡埛閿?| 杩愯惀鍏ュ彛鐩存帴娓呯悊鍗曚欢瀹㈤攣 |
| 瀹㈤攣绠＄悊 | 閲婃斁鍔ㄤ綔 | wh_item_log | bizType = CUSTOMER_LOCK 鏃ュ織 | 閲婃斁鍔ㄤ綔浼氱户缁啓鍗曚欢浜嬩欢鏃ュ織 |


## 2026-03-31 字印单补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 产品包/销售结算单 | 来源单据下推 | wh_marking_bill | 新建字印单 | 字印单承接产品包或销售结算来源池 |
| 字印单 | illAssociation(...) | wh_pack / wh_sale_bill | markingBillId/markingBillNo | 建单时把字印挂接回来源业务单 |
| 字印单 | illAssociation(...) | wh_transfer_bill | markingBillId/markingBillNo | 同时挂接字印承运调拨单 |
| 字印单 | confirm/marked | wh_item / wh_item_log | 单件锁定 / 释放与日志 | 单件字印审核锁 EPC，完工再释放 |

## 2026-03-31 小包补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 产品包/现场配包 | packNo + trayOrder | wh_tray | 生成小包头 | 小包号不是独立流水，而是产品包内分组号 |
| 小包 | 	rayId | wh_tray_detail | 小包明细挂接 | 小包真实内容由明细承载 |
| 小包查询 | getTray/getTrayListByPackNo | wh_item / 配置中心 | 重量补数 / 不干胶重量补数 | 页面展示不是纯主表快照 |
| 销售结算 | 	rayNo | 产品包明细/销售明细 | 按小包结算分组 | 销售链会继续消费小包维度 |

## 2026-03-31 小包明细与字印单明细补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 小包 | 	rayId | wh_tray_detail | 小包内容挂接 | EPC、重量、工费快照真实落在明细层 |
| 小包明细 | 批量更新 | wh_tray 展示链 | 头表展示依赖明细内容 | 头表不是完整事实源 |
| 字印单 | illNo | wh_marking_detail | 字印明细挂接 | 字印 EPC 明细真实落在明细层 |
| 字印单 | marked/obsolete | wh_marking_detail | printStatus/effective 批量回写 | 字印完工和作废都会整单写明细 |

## 2026-03-31 产品包明细与成品转料明细补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 产品包 | 建包/改单 | wh_pack_detail | 包内事实明细 | EPC、小包归组、工费和字印标记都落在这里 |
| 产品包明细 | marking = NEED | markingbill | 待字印来源池 | 字印单直接从产品包明细筛待字印数据 |
| 成品转料单 | 建单/重建 | wh_goods_recycle_detail | 转料事实明细 | 转了哪些成品、多少数量和金重都落在这里 |
| 成品转料明细 | pageData/listData | 报表/导出 | 明细+汇总展示 | 报表层直接消费明细宿主 |

## 2026-03-31 收款分账明细与订单核销明细补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 收款单分账 | 分账结果 | wh_receivable_divide_bill | 分账结果落表 | 钱包归属拆分结果真实落在分账子表 |
| 订单核销单 | 核销执行 | wh_order_writeoff_detail | 核销事实明细 | 本次核销到哪条需求明细都落在这里 |
| 核销明细报表 | cutoffWriteoffSum + writeoffNum | 报表页/导出 | 总核销量展示 | 报表层现场重算累计核销量 |
| 核销明细引用页 | goodsSkuNo 补数 | 成品调拨来源页 | 转成品引用视图 | 不是全量明细都能进入引用池 |

## 2026-03-31 配件域补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 辅料类别 / 分类 / 商品 | `code/categoryNo/goodsNo` | 辅料执行明细 | `accessoryNo/accessoryName` 等快照字段 | 主数据层为入库/出库/库存提供来源口径 |
| 辅料入库单 | 建单/改单 | `wh_accessory_entry_bill_detail` | 明细事实落表 | 金额、数量和辅料编号快照真实落在明细层 |
| 辅料入库单 | `referencesDetails(...)` | 半成品调拨链 | `semisTransferNo` 挂接 | 入库明细可被半成品调拨来源池引用 |
| 辅料出库单 | 建单/改单 | `wh_accessory_out_bill_detail` | 明细事实落表 | 出库数量和金额快照落在明细层 |
| 辅料入库/出库 | 审核/反审 | 单头 | `status/approvedMan/approvedTime` | 当前代码明确可见的是单据状态流回写 |
| 辅料库存 | `shopNo/wareNo/categoryNo/goodsNo` | 查询/日志 | 库存结果口径 | 库存主表与日志表已存在，但主执行链写入仍待确认 |

## 2026-03-31 资金主数据补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 客户 / 供应商档案 | `accountId` | `wh_account` | 账户桥接 | 客户与供应商财务链先落到账户层 |
| 账户 | `deleteAccount(...)` | `wh_wallet` | 按账户删钱包 | 账户删除会直接级联删除钱包 |
| 各类财务单据 | `WalletBaseDTO` | `wh_wallet` | 余额增减 | 钱包是真实资金/重量变更宿主 |
| 钱包变更 | `doChangeWallet(...)` | `wh_wallet_log` | 批量写日志 | 钱包事件表由钱包宿主统一落日志 |
| 收款/付款/内部转账 | 银行侧资金动作 | `wh_shop_bank` | 银行卡余额变化 | 网点银行卡是银行侧主账 |
| 银行卡转账 | `transferBalance(...)` | `wh_shop_bank_log` / `wh_shop_bank_daily` | 双边日志与日报补刷 | 转账同时生成流水并补日结 |

## 2026-03-31 组织与库存主数据补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 网点 | `deptId/shopIds/default` | 仓库 / 银行卡 / 单据 | 网点归属和可见范围 | 网点是大量业务单和库存的基础组织维度 |
| 业务分区 | `areaId/managerUserId` | 业务员 / 客户 / 订单 | 分区经理和组织过滤 | 客户和业务员域会继续补 areaName/manager 信息 |
| 业务员 | `userId/areaId/deptId` | 客户 / 订单 / App | 业务员归属与岗位识别 | 业务员域承接用户到业务组织的桥接 |
| 原料档案 | `materialNo/walletTypeNo` | 钱包 / 原料库存 / 原料日志 | 原料与钱包类型映射 | 原料已经和资金链绑定 |
| 成品库存 | `doUpdateGoodsStock(...)` | `wh_goods_stock_log` | 批量写库存日志 | 成品库存宿主统一生成日志 |
| 原料库存 | `updateMaterialStock(...)` | `wh_material_stock_log` / `wh_material_stock_daily` | 日志与日报回写 | 原料库存宿主统一写日志并补日报 |

## 2026-04-01 报表与任务补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 单件盘点单 | 建任务/提交任务 | `wh_inventory_task` / `wh_inventory_task_detail` | 任务与明细落表 | 盘点执行层从单头继续下钻到任务层 |
| 盘点任务 | `scanStatus` / 盘盈保存 | 盘点任务明细 | 状态推进与盘盈池 | 盘盈数据真实落在任务明细层 |
| 打印任务单 | 建单/审核/完成 | `wh_print_task` | 任务状态推进 | 打印链有自己的执行状态机 |
| 打印任务明细 | `updatePrintCount(...)` | `wh_item` | 单件打印状态更新 | 打印成功会继续改单件域 |
| 即时库存报表 | `skuStockDailyService/reportAllService` | `stockreport` / `stockreportdetail` | 报表聚合与钻取 | 报表层真实来源不在薄 service 本体 |

## 2026-04-01 结算与调拨明细层补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 结价单审核 | `confirm(...)` | `wh_wallet` / `wh_wallet_log` | 现金欠款增加、材质欠料减少 | 审核时真实走双钱包回写，不只是改单头状态 |
| 结价单反审 | `unConfirm(...)` | `wh_wallet` / `wh_wallet_log` | 现金回退、材质回补 | 反审整条镜像回滚 |
| 销售单 | 结算明细整批写入 | `wh_sale_settle` | 销售结算事实落表 | 主单重建时按 billId 整批删改 |
| 退货单 | 结算明细整批写入 | `wh_return_settle` | 退货结算事实落表 | 展示时再按退货业务类型补结算方式 |
| 需求单明细分页 | 委外明细聚合 | `wh_outsourcing_order_detail` | `finishReason/factoryAnswer/orderNo` 聚合展示 | 需求单详情页直接回看委外信息 |
| 单件调拨明细 | 导出/工序调拨报表 | `wh_transfer_bill_detail` | 明细 + 汇总行 | 调拨报表真实宿主在 detail 层 |
| 成品调拨明细 | 按 billId/billNo 查询 | `wh_goods_transfer_bill_detail` | 成品调拨事实层 | 主单详情和导出都依赖它 |

## 2026-04-01 财务明细卫星层补充回写
| 来源模块 | 来源字段/动作 | 目标模块 | 目标字段/动作 | 说明 |
|---------|---------------|---------|---------------|------|
| 收款单 | 建单/改单 | `wh_receivable_bill_detail` | 收款明细整批落表 | 银行侧分笔事实挂在 detail 层 |
| 收款单明细导出 | `selectListData + selectListSummary` | 导出页 | 明细 + 汇总行 | 导出层直接消费 detail 表 |
| 付款单 | 建单/改单 | `wh_payable_bill_detail` | 付款明细整批落表 | 付款分笔事实挂在 detail 层 |
| 客户应收调整单 | 主单回读/分页 | `wh_customer_adjust_bill_detail` | 调整明细与权限过滤 | detail 层直接承接角色可见性 |
| 供应商应付调整单 | 主单回读/分页 | `wh_supplier_adjust_bill_detail` | 调整明细展示 | bizType 文案在 detail 层翻译 |
| 调账单 | 建单/重建 | `wh_account_adjust_bill_detail` | 双分录明细落表 | 调入/调出事实挂在 detail 层 |
| 单件调整单 | 建单/重建 | `wh_adjust_bill_detail` / `wh_adjust_bill_detail_accessories` | 调整明细 + 配件明细落表 | SKU 展示和配件卫星都挂在 detail 层 |

## 执行明细与日志卫星层补充
- `entrybilldetail`
  - 审核后把 `itemId/epc/itemNo` 反写回单件入库明细，成为 `entrybill -> wh_item` 的事实桥。
- `materialinbounddetail`
  - 通过 `computeForWallet(...)` 和 `computeForStock(...)` 把来料明细拆成钱包链与库存链两套汇总输入。
- `materialoutbounddetail`
  - 通过 `computeForWallet(...)` 和 `computeForStock(...)` 把出料明细拆成钱包链与库存链两套扣减输入。
- `returndetail`
  - 把 `returnbill` 单头、仓库、客户、门店、片区、材质、SKU、品类拼成退货事实视图。
- `goodsinboundbilldetail`
  - 把成品入库和退厂原因映射、统计、总计行收敛到一层。
- `maintaininbilldetail`
  - 承接维修回收入库事实，并提供“最近一次维修回收结果”视图。
- `maintaininbilldetaillog`
  - 维修入库日志生成后会继续自动生成客户留货。
- `maintainoutbilldetail`
  - 承接重复维修、后续时序和反审核断判断。

## 单件日志、盘点卫星与 SKU 库存层补充
- `itemlog`
  - 单件入库、出库、维修、销售、作废后的事件轨迹宿主。
- `iteminventorybillitem`
  - 盘点单件快照、差异页、统计页和盘盈盘亏派生宿主。
- `iteminventorybillstock`
  - 盘点建单时的账面库存快照基线。
- `skustock`
  - SKU 实时库存执行宿主，并向日报层回刷。
- `skustockdaily`
  - SKU 日报宿主，承接日报初始化、报表和差异查询。

## 明细事实层与资金日结卫星补充
- `customerorderdetail`
  - 承接客户订单行需求、样重计算、行级出货状态和看板聚合。
- `goodsrejectbilldetail`
  - 承接成品退厂报表事实、原因映射和总计行输出。
- `goodsrejectrecorddetail`
  - 承接退厂登记池的行级事实和批量回写基础。
- `inventoryweighingdetail`
  - 承接称重明细与盘盈盘亏结果回填。
- `profitlossadjustdetail`
  - 承接盈亏调整明细与原料库存差额汇总。
- `wastagedetail`
  - 承接损耗明细分页、导出与总计行。
- `walletdaily`
  - 承接钱包日结、差异核对与告警。
- `shopbankdaily`
  - 承接银行卡日结、差异核对与告警。

## 规则、日志与记录尾项补充
- `inrejectreason`
  - 成品入库与退厂报表共用的原因字典宿主。
- `skurulelog`
  - SKU 编码落号历史轨迹。
- `skusearchlog`
  - SKU 搜索行为日志。
- `stockmessagerecord`
  - 库存消息发送/处理结果态记录。
- `shareflowrecord`
  - 共享库存同步流水宿主。
- `lmapitransfer`
  - 老庙接口调用痕迹宿主。

## 执行尾项明细补充
- `cancelbilldetail`
  - 承接单件作废明细及单头状态/仓店补数。
- `accessoryapplybilldetail`
  - 承接配件领用行明细。
- `distributionbilldetail`
  - 承接铺货明细、源/目标仓店和来源单补数。
- `shelvesbilldetail`
  - 承接库位调整 EPC 明细与单件补数。
- `unitpricemaintaindetail`
  - 承接单价维护行事实。

## 半成品、转料与客留尾项补充
- `recycledetail`
  - 承接单件转料 EPC 明细、源/目标仓与扫码/ERP 状态。
- `semistransfer`
  - 承接半成品调拨主链、明细和 BOM。
- `materialinventorydetail`
  - 承接原料盘点逐行事实。
- `customerretain`
  - 承接客户留货生成、迁转、打包、结案和回退。

## 价格、盘点与铺货尾项补充
- `distributionbill`
  - 承接铺货主链、单件锁定与状态迁移。
- `recyclebill`
  - 承接单件转料主链及成品/原料库存回写。
- `inventorybill`
  - 承接旧版盘点单头与盘盈盘亏汇总。
- `materialinventory`
  - 承接原料盘点单头。
- `materialinboundsettle`
  - 承接来料结算事实与结算补数。
- `unitpricemaintain`
  - 承接单价维护宿主并回写正式价格。
- `unitprice`
  - 承接正式单价主档。
- `walletcredit`
  - 承接账户授信额度主档。

## 商品与 SKU 尾项补充
- `categoryfour/categoryfive/categoryspec`
  - 承接商品结构链四级、五级分类与品类规格主数据。
- `bigweightsegment`
  - 承接大类重量段规则。
- `skufavorite`
  - 承接收藏标签与收藏切换。
- `skuseriesmapping`
  - 承接系列与 SKU 映射。
- `skusetmapping`
  - 承接套式与 SKU 映射，并回写 SKU 主档。
- `skugemstone/skuparts`
  - 承接 SKU 宝石/配件卫星。

## 客户与组织尾项补充
- `customercontacts`
  - 承接客户联系人、小程序登录、临时联系人二维码与选客绑定。
- `customerstaffupdate/customerstaffupdatedetail`
  - 承接客户归属迁移申请、审批和前后归属差异事实层。
- `businessareadept`
  - 承接业务分区与部门映射。
- `skushop`
  - 承接 SKU 与网点归属及网点工费索引。
- `exhibition`
  - 承接展销会主档及客户归属快照。
- `qcstaff`
  - 承接质检员工号主数据。

## 客户订单单件与原料品质尾项补充
- `customerorderitem`
  - 承接客户订单履约单件事实、看板聚合、字印回写、销售挂接和余单日报来源。
- `itemsale`
  - 承接单件销售落点卫星。
- `materialquality`
  - 承接原料品质规则主数据及多编码映射。

## 资金与消息尾项补充
- `goldpricesetting`
  - 承接我的金价、时段规则和调价日志。
- `internalremittance`
  - 承接银行卡内部转账主链及其审核/反审落账。
- `ordermessage`
  - 承接客户订单提醒、业务员/分区领导待办。

## 盘点批次与历史库存尾项补充
- `inventoryweighingbillstock`
  - 承接盘点称重单的库存备份快照。
- `iteminventorybillbatch`
  - 承接单件盘点单的批次层。
- `itemhistorystock`
  - 承接单件每日库存快照及重量段汇总快照。

## 辅石与配件卫星尾项补充
- `entrybilldetailaccessories`
  - 承接单件入库明细辅石快照。
- `itemaccessories`
  - 承接正式单件辅石事实。
- `supplierskuaccessories/supplierskupart`
  - 承接供应商 BOM 的辅石/配件卫星层。

## 分析报表尾项补充
- `supplierqualityreport`
  - 承接供应商品质分析结果。
- `departmentlossreport`
  - 承接部门折损分析结果和日报总损耗聚合。

## 预处理与占料分析尾项补充
- `saleprepossessing`
  - 承接客户订单结算前预处理，并联动订单消息。
- `reportcustomerareaowe`
  - 承接客户/业务分区日均占料分析结果与存储过程报表壳。

## 柜台与周转率报表尾项补充
- `reporttransferwarehouse`
  - 承接库存销售周转率结果与存储过程报表壳。
- `salereportware`
  - 承接柜台/仓库/客户/分区/业务员多维销售统计结果。
