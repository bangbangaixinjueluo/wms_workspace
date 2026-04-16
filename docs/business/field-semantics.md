# 瀛楁璇箟琛?> 褰撳墠瑕嗙洊鑼冨洿锛氬凡鍒嗘瀽鐨勬牳蹇冧氦鏄撻摼妯″潡锛涘熀浜?commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`锛屾棩鏈燂細2026-03-31

## 璇存槑
- 杩欓噷鍙敹褰曞悗缁?AI 鏈€瀹规槗璇垽鐨勫叧閿瓧娈点€?- 姣忎釜瀛楁閮藉敖閲忕粰鍑衡€滀粠鍝噷琚祴鍊?鍒ゆ柇鈥濊繖涓€灞傝涔夛紝鑰屼笉鏄彧缁欒嚜鐒惰瑷€瑙ｉ噴銆?
## 瀛楁璇箟鎬昏〃
| 琛?瀛楁 | 璇箟 | 鍏稿瀷鍙栧€?闃舵 | 璧嬪€?鍒ゆ柇璇佹嵁 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|
| `wh_customer_order.order_status` | 璁㈠崟涓荤姸鎬侊紝鐢辫鍗曟槑缁嗚仛鍚堥噸绠?| `UN_SUBMIT`銆乣WAIT_PICKING`銆乣WAIT_COMEBACK`銆乣WAIT_SETTLE`銆乣SETTLING`銆乣SETTLED`銆乣DELIVERED`銆乣DISCARD` | `submit()` 鐩存帴鍐?`WAIT_PICKING`锛沗customerOrderStatusChange()` 鎸夋槑缁嗙姸鎬佽仛鍚堟敼鍐?| [customerorder.md](/D:/ws/code/wms-api/docs/business/customerorder.md) |
| `wh_customer_order.out_order_status` | 澶栭儴鍑鸿揣渚х姸鎬?| `RECEIVED_ORDER`銆乣ON_DELIVERY`銆乣SHIPPED`銆乣DELIVERY_FAIL` | 璁㈠崟鐘舵€侀噸绠楀拰鍑鸿揣鍥炶皟閮戒細鏀瑰啓 | [customerorder.md](/D:/ws/code/wms-api/docs/business/customerorder.md)銆乕deliverybill.md](/D:/ws/code/wms-api/docs/business/deliverybill.md) |
| `wh_customer_order_item.local_status` | 璁㈠崟鏄庣粏鏈湴涓氬姟鐘舵€侊紝鍐冲畾璁㈠崟涓荤姸鎬?| `PICKING`銆乣RETAIN`銆乣BILLING`銆乣SETTLEMENT`銆乣BILLED`銆乣SHIPPED` | 鎰忓悜鍗曘€佷骇鍝佸寘銆佺粨绠楀崟銆佸嚭璐ч摼閮戒細鎺ㄨ繘鎴栧洖閫€ | [customerorder-intent.md](/D:/ws/code/wms-api/docs/business/customerorder-intent.md)銆乕pack.md](/D:/ws/code/wms-api/docs/business/pack.md)銆乕salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| `wh_customer_retain.status` | 瀹㈢暀瀛樹富鐘舵€?| `STORAGE`銆乣ONGOING`銆乣RECYCLED` | 鍒涘缓/淇敼瑕佹眰 `STORAGE`锛涙墦鍖呭啓 `ONGOING`锛涘彇娑堝啓 `RECYCLED` | [customerretain.md](/D:/ws/code/wms-api/docs/business/customerretain.md) |
| `wh_customer_retain.pack_no` | 瀹㈢暀瀛樼粦瀹氱殑浜у搧鍖呭崟鍙?| 褰撳墠浜у搧鍖呭崟鍙?| `packing()` 鍐欏叆锛岃В缁?鍙栨秷浜у搧鍖呮椂娓呯悊鎴栧洖閫€ | [pack.md](/D:/ws/code/wms-api/docs/business/pack.md) |
| `wh_pack.pack_status` | 浜у搧鍖呬富鐘舵€?| `INIT`銆乣CONFIRM`銆乣INFIRM`銆乣DISCARD`銆乣RECEIVED` | 寤哄寘/瀹℃牳/鍙嶅/浣滃簾/缁撶畻瀹℃牳鑱斿姩 | [pack.md](/D:/ws/code/wms-api/docs/business/pack.md) |
| `wh_pack.transfer_status` | 浜у搧鍖呭湪璋冩嫧閾句腑鐨勬簮鍗曟嵁鐘舵€?| `INIT`銆乣BE_TRANSFERRED`銆乣SIGNED`銆乣REJECTED_SIGNED`銆乣RETURNED_SIGNED` | 璋冩嫧瀹℃牳銆佹帴鏀躲€佹嫆绛俱€侀€€鍥炪€佸弽瀹°€佷綔搴熷洖鍐?| [pack.md](/D:/ws/code/wms-api/docs/business/pack.md)銆乕transferbill.md](/D:/ws/code/wms-api/docs/business/transferbill.md)銆乕goodstransferbill.md](/D:/ws/code/wms-api/docs/business/goodstransferbill.md) |
| `wh_pack.transferBillId/transferBillNo` | 褰撳墠鎸傛帴鐨勮皟鎷ㄥ崟涓婚敭/鍗曞彿 | 璋冩嫧鍗曟爣璇?| 璋冩嫧瀹℃牳鍐欏叆锛屼綔搴熸椂娓呯┖ | [pack.md](/D:/ws/code/wms-api/docs/business/pack.md) |
| `wh_pack.sale_bill_id/sale_bill_no` | 褰撳墠鎸傛帴鐨勭粨绠楀崟涓婚敭/鍗曞彿 | 缁撶畻鍗曟爣璇?| 寤虹珛/鍙栨秷缁撶畻鍏崇郴鏃舵敼鍐?| [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| `wh_sale_bill.bill_status` | 缁撶畻鍗曚富鐘舵€?| `INIT`銆乣CONFIRM`銆乣INFIRM`銆乣DISCARD`銆乣DELIVERY_ALLOW`銆乣CONFIRM_ALLOW_DELIVERY`銆乣CONFIRM_NO_DELIVERY`銆乣DELIVERED` | 缁撶畻瀹℃牳銆佸彂璐х敵璇枫€佺‘璁ゅ彲鍙戣揣銆佸疄闄呭彂璐с€侀噸鍋?| [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md) |
| `wh_sale_bill.transferStatus` | 缁撶畻鍗曞湪鎴愬搧璋冩嫧閾句腑鐨勮皟鎷ㄧ姸鎬?| 涓庝骇鍝佸寘 `transfer_status` 鍚岀被璇箟 | 浠呭湪鎴愬搧璋冩嫧婧愬崟鎹槸缁撶畻鍗曟椂鍥炲啓 | [goodstransferbill.md](/D:/ws/code/wms-api/docs/business/goodstransferbill.md) |
| `wh_transfer_bill.bill_status` | 鍗曚欢璋冩嫧鍗曚富鐘舵€?| `INIT`銆乣CONFIRMING`銆乣CONFIRM`銆乣INFIRM`銆乣DISCARD`銆乣RECEIVED`銆乣REJECTED`銆乣RETURNING`銆乣BACK_RECEIVED`銆乣BACK_REJECTED` | 璋冩嫧鍚勫姩浣滅洿鎺ュ垽鏂?鏀瑰啓 | [transferbill.md](/D:/ws/code/wms-api/docs/business/transferbill.md) |
| `wh_goods_transfer_bill.bill_status` | 鎴愬搧璋冩嫧鍗曚富鐘舵€?| 涓庡崟浠惰皟鎷ㄥ崟澶嶇敤鍚屼竴濂楃姸鎬佹満 | 瀹℃牳銆佹帴鏀躲€侀€€鍥炪€佸弽瀹°€佷綔搴?| [goodstransferbill.md](/D:/ws/code/wms-api/docs/business/goodstransferbill.md) |
| `wh_delivery_bill.status` | 鍑鸿揣鍚屾鐘舵€?| `WAIT_DELIVERY`銆乣IN_DELIVERY`銆乣DELIVERY_FAIL`銆乣DELIVERY_SUCCESS` | 鍙戣揣璇锋眰鍜屽洖璋冨垎鍒帹杩?| [deliverybill.md](/D:/ws/code/wms-api/docs/business/deliverybill.md) |
| `wh_customer_order.outPpOrderNo` | 澶栭儴 PP/鐗╂祦鍗曞彿 | 鐢?`trackingNo` 鍥炲啓 | `TYPE_B` 鍑鸿揣鏃跺湪 `ship/reship` 鍓嶅啓鍏?| [deliverybill.md](/D:/ws/code/wms-api/docs/business/deliverybill.md) |

## 鍚箟涓嶆槑
| 瀛楁 | 褰撳墠鐤戠偣 | 澶勭悊寤鸿 |
|------|------|------|
| `wh_customer_retain.status = RECYCLED` | 鏂囨鏄€滈噸鏂颁笂鏌溾€濓紝浣嗙姸鎬佸悕鏇村儚鈥滃凡鍥炴敹鈥?| 淇濈暀 `鈿狅笍`锛屽緟涓氬姟纭 |
| `wh_sale_bill.bill_status = DELIVERY_ALLOW` | 鎺ュ彛鍚嶆槸鈥滃彂璐х敵璇封€濓紝浣嗘湭鐪嬪埌 `DELIVERY_REQUESTING` 鐪熸浣跨敤 | 淇濈暀 `鈿狅笍`锛屽緟涓氬姟纭 |
| `OrderWriteoffApiImpl.updateProcessResult()` | 鏆撮湶浜?`oa_orderWriteoff` 娴佺▼ key锛屼絾瀹℃壒鍥炶皟瀹為檯瀹炵幇琚敞閲婏紝鏃犳硶纭娴佺▼瀛楁濡備綍钀藉崟 | 淇濈暀 `鈿狅笍`锛屽緟纭 BPM 鏄惁浠嶅湪浣跨敤 |

## 璁㈠崟鏍搁攢琛ュ厖瀛楁
| 琛?瀛楁 | 璇箟 | 鍏稿瀷鍙栧€?/ 闃舵 | 璧嬪€?/ 鍒ゆ柇璇佹嵁 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|
| `wh_order_writeoff.order_status` | 鏍搁攢鍗曞ご鐘舵€?| `INIT`銆乣CONFIRM`銆乣INFIRM`銆乣DISCARD` | 浠呰〃绀哄崟鎹槸鍚﹀凡瀹℃牳/鍙嶅/浣滃簾锛屼笉琛ㄧず鈥滈儴鍒嗘牳閿€/鏍搁攢瀹屾瘯鈥?| [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |
| `wh_order_writeoff_detail.writeoff_num/writeoff_gram_weight` | 鏈鏍搁攢閲?| 绛変簬 `passNum/passGramWeight` | `handleConvertDetail()` 涓洿鎺ョ敱 `returned - bad` 璁＄畻鍚庤祴鍊硷紝涓嶆槸鎬诲洖璐ч噺 | [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |
| `wh_order_writeoff_detail.returned_num/returned_weight` | 鏈鍥炶揣鎬婚噺 | 鍚悎鏍间笌涓嶅悎鏍?| 鐢ㄤ簬鎷嗗嚭鍚堟牸閲忎笌涓嶅悎鏍奸噺锛屽苟鍚屾鍥炲啓濮斿鏄庣粏鎬诲洖璐х疮璁?| [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |
| `wh_order_writeoff_detail.bad_writeoff_num/bad_writeoff_gram_weight` | 鏈涓嶅悎鏍奸噺 | 浠庢湰娆″洖璐т腑鍓ョ鐨勪笉鍚堟牸閮ㄥ垎 | 涓?`returned*` 涓€璧峰喅瀹?`pass*` 鍜?`writeoff*` | [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |
| `wh_outsourcing_order.order_status` | 濮斿鍗曟牳閿€杩涘害鐘舵€?| `CONFIRM(寰呮牳閿€)`銆乣CANCELLATION(宸查儴鍒嗘牳閿€)`銆乣DELIVERED(鏍搁攢瀹屾瘯)`銆乣FINISH(缁堟鏍搁攢)` | 璁㈠崟鏍搁攢瀹℃牳/鍙嶅鍚庢寜鍏ㄩ噺濮斿鏄庣粏 `totalWriteoffNum` 鑱氬悎閲嶇畻 | [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |
| `wh_demand_order_detail.returned_num/returned_weight` | 闇€姹傚崟渚х疮璁℃牳閿€閲?| 瀹為檯鐢扁€滃悎鏍兼牳閿€閲忊€濈疮璁¤€屾潵 | `writeOffDemandOrder()` 鐢?`writeoff*` 澧炲噺锛屼唬鐮佷笂骞朵笉鏄疮璁℃€诲洖璐ч噺 | [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md) |

## 鏀舵鍗曡ˉ鍏呭瓧娈?| 琛?瀛楁 | 璇箟 | 鍏稿瀷鍙栧€?/ 闃舵 | 璧嬪€?/ 鍒ゆ柇璇佹嵁 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|
| `wh_receivable_bill.bill_status` | 鏀舵鍗曞ご鐘舵€?| `INIT`銆乣CONFIRM`銆乣DIVIDE`銆乣DISCARD` | 骞堕潪鏅€氭湭瀹?鍙嶅妯″瀷锛涘垎璐︽€佹槸鏄惧紡鐘舵€侊紝鍙嶅鐩存帴鍥炲埌 `INIT` | [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md) |
| `wh_receivable_bill.total_amount` | 鏀舵閲戦鍚堣 | 鎸夋槑缁嗘眹鎬诲悗鐨?2 浣嶅皬鏁板€?| `calcAndCheckSubmitValues()` 浠ュ悗绔槑缁嗘眰鍜屼负鍑?| [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md) |
| `wh_receivable_bill_detail.amount` | 鍗曟潯鏀舵娓犻亾/閾惰鏀舵閲戦 | 鍗曠瑪鍏ヨ处閲戦 | 瀹℃牳/鍙嶅鏃堕€愭潯椹卞姩閾惰鍗′綑棰濆拰閾惰鏃ュ織鍙樺姩 | [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md) |
| `wh_wallet.balance`锛堟敹娆鹃摼璺級 | 閽卞寘璧勯噾浣欓 | 瀹℃牳涓庡垎璐﹀悗鍙樺寲 | 瀹℃牳/鍒嗚处缁熶竴鐢?`diffBalance = -amount` 璋冩暣锛屽弽瀹?鍙栨秷鍒嗚处鍐嶅仛鍙嶅悜鍥炴粴 | [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md) |
| `wh_receivable_divide_bill.total_amount` | 鍒嗚处鍚庢煇璐︽埛鎵挎帴鐨勯噾棰?| 澶氳处鎴锋媶鍒嗛噾棰?| `divide/unDivide` 鏃朵綔涓洪挶鍖呭綊灞為噸鍒嗛厤鐨勬寔涔呭寲璁板綍 | [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md) |

## 浠樻鍗曡ˉ鍏呭瓧娈?| 琛?瀛楁 | 璇箟 | 鍏稿瀷鍙栧€?/ 闃舵 | 璧嬪€?/ 鍒ゆ柇璇佹嵁 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|
| `wh_payable_bill.bill_status` | 浠樻鍗曞ご鐘舵€?| `INIT`銆乣CONFIRM`銆乣INFIRM`銆乣DISCARD` | 瀹℃牳鏀?`CONFIRM`锛屽弽瀹℃敼 `INFIRM`锛屼綔搴熸敼 `DISCARD` | [payablebill.md](/D:/ws/code/wms-api/docs/business/payablebill.md) |
| `wh_payable_bill.total_amount` | 浠樻閲戦鍚堣 | 鎸夋槑缁嗘眹鎬诲悗鐨?2 浣嶅皬鏁伴噾棰?| `calcAndCheckSubmitValues()` 浠ュ悗绔槑缁嗘眰鍜屼负鍑?| [payablebill.md](/D:/ws/code/wms-api/docs/business/payablebill.md) |
| `wh_payable_bill.biz_type` | 浠樻涓氬姟瀵硅薄绫诲瀷 | `2701` 浠樺鎴锋銆乣2702` 浠樹緵搴斿晢娆俱€乣2703` 璐圭敤鎶ラ攢 | `setCustomerInfo()` 鎸夎瀛楁璧板鎴?渚涘簲鍟嗕笉鍚屽洖濉€昏緫 | [payablebill.md](/D:/ws/code/wms-api/docs/business/payablebill.md) |
| `wh_payable_bill_detail.amount` | 鍗曟潯浠樻娓犻亾閲戦 | 鏌愬紶閾惰鍗℃湰娆″疄闄呬粯娆鹃噾棰?| `doShopBankLog()` 鎸夋瘡鏉℃槑缁嗛噾棰濋€愭潯鎵ｅ噺/鍔犲洖閾惰鍗′綑棰?| [payablebill.md](/D:/ws/code/wms-api/docs/business/payablebill.md) |
| `wh_wallet.balance`锛堜粯娆鹃摼璺級 | 閽卞寘璧勯噾浣欓 | 瀹℃牳鍚庡鍔狅紝鍙嶅鍚庢墸鍥?| 瀹℃牳鍙?`diffBalance = totalAmount`锛屽弽瀹″彇 `diffBalance = -totalAmount` | [payablebill.md](/D:/ws/code/wms-api/docs/business/payablebill.md) |
| `wh_wallet_log.accounting_type`锛堜粯娆鹃摼璺級 | 閽卞寘鏃ュ織璁拌处鍙ｅ緞 | 瀹℃牳 `DEBT`锛屽弽瀹?`DEPOSIT` | `confirm/unConfirm` 鍒嗗埆鍐欐涓嶅悓 `WalletAccountingTypeEnum` | [payablebill.md](/D:/ws/code/wms-api/docs/business/payablebill.md) |

## 瀹㈡埛搴旀敹璐﹁皟鏁磋ˉ鍏呭瓧娈?| 琛?瀛楁 | 璇箟 | 鍏稿瀷鍙栧€?/ 闃舵 | 璧嬪€?/ 鍒ゆ柇璇佹嵁 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|
| `wh_customer_adjust_bill.bill_status` | 瀹㈡埛搴旀敹璐﹁皟鏁村崟澶寸姸鎬?| `INIT`銆乣CONFIRM`銆乣INFIRM`銆乣DISCARD` | 瀹℃牳鏀?`CONFIRM`锛屽弽瀹℃敼 `INFIRM`锛屼綔搴熸敼 `DISCARD` | [customeradjustbill.md](/D:/ws/code/wms-api/docs/business/customeradjustbill.md) |
| `wh_customer_adjust_bill.rebate` | 鍗曞ご杩斿埄鍊?| 2 浣嶅皬鏁拌繑鍒╅噾棰?| 鍒涘缓/鏇存柊鏃剁粺涓€ `setHalfUp(..., 2)` | [customeradjustbill.md](/D:/ws/code/wms-api/docs/business/customeradjustbill.md) |
| `wh_customer_adjust_bill_detail.amount` | 鏈潯搴旀敹閲戦璋冩暣鍊?| 鐜伴噾鍙ｅ緞閲戦澧炲噺 | 瀹℃牳/鍙嶅鏃跺浐瀹氳蛋鐜伴噾閽卞寘锛歚walletTypeNo = CASH`銆乣settleClass = SETTLE_CASH` | [customeradjustbill.md](/D:/ws/code/wms-api/docs/business/customeradjustbill.md) |
| `wh_customer_adjust_bill_detail.gold_weight` | 鏈潯搴旀敹閲戦噸璋冩暣鍊?| 鏉愯川鍙ｅ緞閲嶉噺澧炲噺 | 瀹℃牳/鍙嶅鏃舵寜 `materialId + walletTypeNo + settleClass` 钀芥潗璐ㄩ挶鍖?| [customeradjustbill.md](/D:/ws/code/wms-api/docs/business/customeradjustbill.md) |
| `wh_customer_adjust_bill_detail.wallet_type_no` | 閲戦噸璋冩暣瀵瑰簲鐨勯挶鍖呯被鍨嬬紪鐮?| 鏉ヨ嚜鏉愯川閽卞寘绫诲瀷 | 瀵煎叆鏃舵寜鏉愯川鍥炲～锛屽鏍?鍙嶅鏃剁洿鎺ョ敤浜庢潗璐ㄩ挶鍖呰惤璐?| [customeradjustbill.md](/D:/ws/code/wms-api/docs/business/customeradjustbill.md) |
| `wh_wallet_log.accounting_type`锛堝鎴峰簲鏀惰处璋冩暣閾捐矾锛?| 閽卞寘鏃ュ織璁拌处鍙ｅ緞 | 瀹℃牳 `DEPOSIT`锛屽弽瀹?`DEBT` | 搴旀敹璋冩暣瀹℃牳浠ｈ〃瀹㈡埛娆犳鍑忓皯锛屽弽瀹′唬琛ㄦ妸娆犳鎸傚洖 | [customeradjustbill.md](/D:/ws/code/wms-api/docs/business/customeradjustbill.md) |

## 浠樻閾捐矾琛ュ厖鐤戠偣
| 瀛楁 | 褰撳墠鐤戠偣 | 澶勭悊寤鸿 |
|------|------|------|
| `wh_wallet_log.accounting_type`锛堜粯娆鹃摼璺級 | 瀹℃牳鍐?`DEBT`銆佸弽瀹″啓 `DEPOSIT`锛岃涔変笂鏇村儚鈥滄鍚戜粯娆?閫嗗悜鍐插洖鈥濓紝浣嗗懡鍚嶄笌瀹為檯閽卞寘浣欓鏂瑰悜浠嶉渶涓氬姟纭 | 淇濈暀 `鈿狅笍`锛屽悗缁仈鍚岄挶鍖呭彴璐﹀彛寰勪竴璧风‘璁?|
## 渚涘簲鍟嗗簲浠樿处璋冩暣琛ュ厖瀛楁
| 琛?/ 瀛楁 | 璇箟 | 鍏稿瀷鍙栧€?/ 闃舵 | 璧嬪€?/ 鍒ゆ柇璇佹嵁 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|
| `wh_supplier_adjust_bill.bill_status` | 渚涘簲鍟嗗簲浠樿处璋冩暣鍗曞ご鐘舵€?| `INIT`銆乣CONFIRM`銆乣INFIRM`銆乣DISCARD` | 瀹℃牳鏀?`CONFIRM`锛屽弽瀹℃敼 `INFIRM`锛屼綔搴熸敼 `DISCARD` | [supplieradjustbill.md](/D:/ws/code/wms-api/docs/business/supplieradjustbill.md) |
| `wh_supplier_adjust_bill_detail.amount` | 鏈潯搴斾粯閲戦璋冩暣鍊?| 鐜伴噾鍙ｅ緞閲戦澧炲噺 | 瀹℃牳/鍙嶅鏃跺浐瀹氳蛋鐜伴噾閽卞寘锛宍walletTypeNo = CASH`銆乣settleClass = SETTLE_CASH` | [supplieradjustbill.md](/D:/ws/code/wms-api/docs/business/supplieradjustbill.md) |
| `wh_supplier_adjust_bill_detail.gold_weight` | 鏈潯搴斾粯閲戦噸璋冩暣鍊?| 鏉愯川鍙ｅ緞閲嶉噺澧炲噺 | 瀹℃牳/鍙嶅鏃舵寜 `materialId + walletTypeNo + settleClass` 钀芥潗璐ㄩ挶鍖?| [supplieradjustbill.md](/D:/ws/code/wms-api/docs/business/supplieradjustbill.md) |
| `wh_supplier_adjust_bill_detail.wallet_type_no` | 閲戦噸璋冩暣瀵瑰簲鐨勯挶鍖呯被鍨嬬紪鐮?| 鏉ヨ嚜鏉愯川閽卞寘绫诲瀷 | 瀵煎叆鏃舵寜鏉愯川鍥炲～锛屽鏍?鍙嶅鏃剁洿鎺ョ敤浜庢潗璐ㄩ挶鍖呰惤璐?| [supplieradjustbill.md](/D:/ws/code/wms-api/docs/business/supplieradjustbill.md) |
| `wh_wallet_log.accounting_type`锛堜緵搴斿晢搴斾粯璋冩暣閾捐矾锛?| 閽卞寘鏃ュ織璁拌处鍙ｅ緞 | 瀹℃牳 `DEPOSIT`锛屽弽瀹?`DEBT` | 搴斾粯璋冩暣瀹℃牳浠ｈ〃渚涘簲鍟嗘寕璐﹀噺灏戯紝鍙嶅浠ｈ〃鎶婃寕璐︽寕鍥?| [supplieradjustbill.md](/D:/ws/code/wms-api/docs/business/supplieradjustbill.md) |

## 渚涘簲鍟嗗簲浠樿处璋冩暣琛ュ厖鐤戠偣
| 瀛楁 | 褰撳墠鐤戠偣 | 澶勭悊寤鸿 |
|------|------|------|
| `wh_supplier_adjust_bill_detail.gold_weight` | 浠ｇ爜淇濆瓨鏃朵繚鐣?2 浣嶅皬鏁帮紝浣嗚〃瀛楁瀹氫箟涓?`decimal(10,3)` | 淇濈暀 `鈿狅笍`锛屽緟纭鏄惁搴旂粺涓€鎴?3 浣嶅皬鏁?|
| `unConfirm` 寮傚父鐮?| 渚涘簲鍟嗗弽瀹″垎鏀粛鎶涘鎴蜂晶閿欒鐮侊紝璇箟涓庢ā鍧椾笉涓€鑷?| 淇濈暀 `鈿狅笍`锛屽緟纭鏄惁搴旀媶鎴愪緵搴斿晢涓撶敤閿欒鐮?|
## 璋冭处鍗曡ˉ鍏呭瓧娈?| 琛?/ 瀛楁 | 璇箟 | 鍏稿瀷鍙栧€?/ 闃舵 | 璧嬪€?/ 鍒ゆ柇璇佹嵁 | 渚濇嵁鏂囨。 |
|------|------|------|------|------|
| `wh_account_adjust_bill.bill_status` | 璋冭处鍗曞ご鐘舵€?| `INIT`銆乣CONFIRM`銆乣INFIRM`銆乣DISCARD` | 瀹℃牳鏀?`CONFIRM`锛屽弽瀹℃敼 `INFIRM`锛屼綔搴熸敼 `DISCARD` | [accountadjustbill.md](/D:/ws/code/wms-api/docs/business/accountadjustbill.md) |
| `wh_account_adjust_bill.sour_account_id/dest_account_id` | 璋冨嚭璐︽埛涓庤皟鍏ヨ处鎴?| 鎴愬璐︽埛涓婚敭 | 瀹℃牳/鍙嶅鏃舵墍鏈夐挶鍖呭姩浣滈兘鍥寸粫杩欎袱涓处鎴烽暅鍍忓睍寮€ | [accountadjustbill.md](/D:/ws/code/wms-api/docs/business/accountadjustbill.md) |
| `wh_account_adjust_bill.total_amount` | 璋冭处閲戦鍚堣 | 鐜伴噾鍙ｅ緞姹囨€诲€?| 瀹℃牳鏃舵媶鎴愨€滆皟鍑鸿礋銆佽皟鍏ユ鈥濅袱绗旂幇閲戦挶鍖呭姩浣?| [accountadjustbill.md](/D:/ws/code/wms-api/docs/business/accountadjustbill.md) |
| `wh_account_adjust_bill.total_gold_weight` | 璋冭处閲戦噸鍚堣 | 鏉愯川鍙ｅ緞姹囨€诲€?| 瀹℃牳鏃舵媶鎴愨€滆皟鍑鸿礋銆佽皟鍏ユ鈥濅袱绗旀潗璐ㄩ挶鍖呭姩浣?| [accountadjustbill.md](/D:/ws/code/wms-api/docs/business/accountadjustbill.md) |
| `wh_account_adjust_bill.material_id` | 閲戦噸璋冭处浣跨敤鐨勬潗璐?| 鏉愯川涓婚敭 | 鐢ㄦ潵瑙ｆ瀽 `materialNo/walletTypeNo`锛岀己澶卞垯閲戦噸閾炬棤娉曡繃璐?| [accountadjustbill.md](/D:/ws/code/wms-api/docs/business/accountadjustbill.md) |

## 璋冭处鍗曡ˉ鍏呯枒鐐?| 瀛楁 | 褰撳墠鐤戠偣 | 澶勭悊寤鸿 |
|------|------|------|
| `wh_account_adjust_bill_detail.gold_weight` | 淇濆瓨鏃朵繚鐣?2 浣嶅皬鏁帮紝浣嗚〃瀛楁瀹氫箟涓?`decimal(10,3)` | 淇濈暀 `鈿狅笍`锛屽緟纭鏄惁搴旂粺涓€鎴?3 浣嶅皬鏁?|
| `confirm/unConfirm/discard` 寮傚父鐮?| 璋冭处鍗曚笉瀛樺湪鏃朵粛鎶涘鎴峰簲鏀惰皟鏁翠晶閿欒鐮?| 淇濈暀 `鈿狅笍`锛屽緟纭鏄惁搴旂粺涓€鍒囧洖璋冭处鍗曚笓鐢ㄩ敊璇爜 |

## 盈亏调整单补充字段
| 表 / 字段 | 语义 | 典型取值 / 阶段 | 赋值 / 判断证据 | 依据文档 |
|------|------|------|------|------|
| `wh_profit_loss_adjust_bill.biz_type` | 盈亏调整业务口径 | `3601` 饰品调整、`3602` 原料调整 | `doSaveDetails()`、`adjustStock()` 都按它分流校验和库存写回链路 | [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md) |
| `wh_profit_loss_adjust_bill.bill_status` | 盈亏调整单头状态 | `INIT`、`CONFIRM`、`INFIRM`、`DISCARD` | `confirm/unConfirm/obsolete` 直接驱动状态机；库存回滚方向也依赖它判断 | [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md) |
| `wh_profit_loss_adjust_bill.total_num` | 盈亏件数合计 | 按明细 `num` 求和 | 创建和更新时都由后端重算，不信任前端直接传值 | [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md) |
| `wh_profit_loss_adjust_bill.total_gold_weight` | 盈亏金重合计 | 按明细 `goldWeight` 求和 | 创建时会过滤 `null`，更新时当前实现未过滤 `null` | [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md) |
| `wh_profit_loss_adjust_detail.num` | 明细盈亏件数 | 正数盘盈、负数盘亏 | `3601` 成品库存调整时原样送入 `updateGoodsStockAdjust(...)` | [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md) |
| `wh_profit_loss_adjust_detail.gold_weight` | 明细盈亏金重 | 正数盘盈、负数盘亏 | 成品侧直接参与库存调整，原料侧参与 `computeForStock(...)` 聚合 | [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md) |
| `wh_profit_loss_adjust_bill.inventory_id/inventory_bill_no/monthly_inventory` | 盘点来源追踪字段 | 盘点单主键、盘点单号、月盘标记 | 当前仅见存储和查询条件，未见进一步自动回写盘点单逻辑 | [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md) |

## 盈亏调整单补充疑点
| 字段 | 当前疑点 | 处理建议 |
|------|------|------|
| `validDetails()` 明细校验 | 代码里已有“件数/金重不能为空且不能为 0”的校验，但创建和更新时都被注释掉 | 保留 `??`，待确认这是故意放宽还是遗留漏接 |
| `wh_profit_loss_adjust_bill.total_gold_weight` | 更新时聚合未过滤 `null`，若前端传空金重可能触发空指针 | 保留 `??`，待确认前端是否永远保证非空，或后端补过滤 |

## 客户来料单补充字段
- `totalStockWeight`：保存的是本单导致的欠料变化量，建单时会直接取负，不等于普通“本次来料重量”。
- `stockWeightNow` / `stockMoneyNow`：建单时读取账户当前钱包余额，表示“截止本单前”的欠料/欠款快照。
- `totalSettleMoney`：客户来料审核/反审时用于单独驱动现金钱包。
- `settleWeight`：客户来料材质钱包主账真正使用的重量口径，来自结算明细，不是直接取明细折算重。

## 供应商来料单补充字段
- `billType=客户来料/供应商来料` 和 `bizType=2001/2201/2202` 必须结合看；同表不同口径。
- `totalPurifyFee`：供应商来料在钱包主账汇总时会先塞入现金钱包编码，再交给工厂分支过滤。
- `goldPrice`：供应商来料结价分支在钱包过账前仍会再次校验，且门槛是大于 200。

## 客户来料单补充疑点
- `getCloseWalletData()` 目前按 `settleName == CLOSING_PRICE.getText()` 识别结价，不是按稳定编码识别。

## 成品入库单补充字段
- `stockWeightNow` / `stockMoneyNow`：建单时读取供应商账户钱包余额，表示截止本单前的欠料/欠款快照。
- `totalCmtFee`：普通成品入库按“克工费 + 件工费”重算；转成品/退料回流还会叠加半成品记录里的 `pickCmtFee + supplyCmtFee`。
- `semisTransferNo`：成品入库明细被半成品调拨引用时写入的占用单号。
- `semisBackTransferNo`：半成品记录被退料/回流链引用时写入的占用单号。

## 成品入库单补充疑点
- `confirm/infirm` 更新库存时统一传 `G_PURCHASE_IN_STORAGE`，需要确认库存日志是否会丢失 `0402/0403` 真实业务语义。

## 客户出料单补充字段
- `totalSettleMoney`：客户出料审核/反审时会先塞进现金钱包编码，再由客户工厂分支决定是否真正保留。
- `totalConvertWeight`：客户买料、卖料出库等金额型分支的关键计价基数。

## 供应商出料单补充字段
- `2301/2302/2303` 的 `settleMoney` 口径分别是 0、`originWeight * goldPrice`、`convertWeight * goldPrice`。
- `stockWeightNow` / `stockMoneyNow`：建单时读取账户当前钱包余额，表示截止本单前的欠料/欠款快照。

## 出料链补充疑点
- `confirm()` 当前通过 `billName` 文案分支做客户/供应商校验，不是按稳定编码判断。

## 瀹㈡埛閫€璐у崟琛ュ厖瀛楁
- `totalSettleMoney` / `totalSettleWeight`锛氶兘鏉ヨ嚜閫€璐х粨绠楁槑缁嗘眹鎬伙紝涓斾繚瀛樻椂鍙栬礋锛屼笉鏄晢鍝佹槑缁嗙洿鎺ユ眹鎬诲€笺€?- `epc`锛氭槸鍚︿负绌哄喅瀹氳蛋鈥滃崟浠跺洖搴撯€濊繕鏄€滄垚鍝佸洖搴撯€濆垎鏀€?- `sourceShopId/sourceWareId/sourceLocalId/sourceItemStatus`锛氫繚瀛橀€€璐у墠鍗曚欢鍘熷浣嶇疆鍜岀姸鎬侊紝渚涘弽瀹℃仮澶嶃€?
## 瀹㈡埛閫€璐у崟琛ュ厖鐤戠偣
- `handlerCloseWallet()` 鐩墠鎸?`settleName == CLOSING_PRICE.getText()` 璇嗗埆缁撲环锛屼笉鏄寜绋冲畾缂栫爜璇嗗埆銆?
## 鎴愬搧閫€鍘傜櫥璁板崟琛ュ厖瀛楁
- `outOfWare`锛氫笉鏄鏍哥姸鎬侊紝鑰屾槸閫€鍘傛墽琛岃繘搴︼紱`NOT_OUT/OUTING/OUTED` 蹇呴』鍜?`billStatus` 鍒嗗紑鐞嗚В銆?- `detailItemType`锛氭爣璇嗚繖寮犵櫥璁板崟鏄€滃崟浠躲€佹垚鍝併€佹贩鍚堚€濆摢涓€绉嶆槑缁嗙粨鏋勩€?- `rejectBillId`锛氫竴鏃﹁鎴愬搧閫€鍘傚崟鍗犵敤锛屼細鍥炲啓褰撳墠鎵ц鍗曚富閿€?
## 鎴愬搧閫€鍘傜櫥璁板崟琛ュ厖鐤戠偣
- `delete()` 褰撳墠鐩存帴鐗╃悊鍒犻櫎锛屾湭瑙佸熀浜?`billStatus/outOfWare` 鐨勪繚鎶ゆ潯浠躲€?
## 鎴愬搧閫€鍘傚崟琛ュ厖瀛楁
- `bizType`锛歚0601` 鏄櫘閫氶€€鍘傦紝`0602` 鏄緵搴斿晢棰嗘枡閫€鍘傦紱涓よ€呭叡浜富琛紝浣嗘潵婧愭寕鎺ュ拰闄勫姞閾句笉鍚屻€?- `totalCmtFee`锛歚0601` 璧版湰鍗曞伐璐瑰叕寮忥紝`0602` 鐩存帴鍙栧崐鎴愬搧璋冩嫧鏄庣粏 `money` 鑱氬悎銆?- `semisTransferId`锛氱敤浜庡悗缁妸渚涘簲鍟嗛鏂欓€€鍘傚拰鍥炴祦/杞垚鍝侀摼缁х画鎸傝捣鏉ャ€?
## 鎴愬搧閫€鍘傚崟琛ュ厖鐤戠偣
- `doOutOfWareRejectRecord()` 鍦ㄥ弽瀹℃椂鎶婂叧鑱旂櫥璁版睜鐘舵€佸洖鍐欐垚 `OUTING`锛岃涔変笂鏇村儚鈥滀粛鍗犵敤浣嗗凡鎾ら攢鍑哄簱鈥濓紝闇€纭鏄惁绗﹀悎涓氬姟棰勬湡銆?

## 鎴愬搧杞枡鍗曡ˉ鍏呭瓧娈?- `materialNo`锛氭暣寮犲崟瑕佽浆鍏ョ殑鐩爣鍘熸枡缂栫爜锛屽師鏂欏簱瀛橀摼缁熶竴鍚冭繖涓瓧娈点€?- `destWareId`锛氱洰鏍囧師鏂欎粨锛屽鏍?鍙嶅鏃跺師鏂欏簱瀛橀兘鍐欒繖閲岋紝涓嶅啓婧愭垚鍝佷粨銆?- `itemNo`锛氭槸鍚︿负绌哄喅瀹氳蛋鎴愬搧鏄庣粏鍒嗘敮杩樻槸鍗曚欢杞枡瀛愰摼銆?- `totalGoldWeightRepeat`锛氳〃澶翠繚鐣欎簡鈥滃疄闄呴噾閲嶅悎璁♀€濆瓧娈碉紝浣嗗綋鍓嶄富閾鹃噷鏈湅鍒版牳蹇冨鏍稿彛寰勭洿鎺ヤ娇鐢ㄥ畠銆?
## 鎴愬搧杞枡鍗曡ˉ鍏呯枒鐐?- `unConfirm()` 鎴愬搧鍒嗘敮鍥炴粴鍘熸枡搴撳瓨鏃跺綋鍓嶄紶鐨勬槸鍏ㄩ噺 `details`锛屼笉鏄?`goodsDetails`锛岄渶纭鏄惁浼氭妸鍗曚欢鏄庣粏閲戦噸涔熷苟鍏ュ師鏂欏洖婊氥€?

## 鍗曚欢鍏ュ簱鍗曡ˉ鍏呭瓧娈?鐤戠偣
- `bizType`锛氬綋鍓嶈嚦灏戣鐩?`0801/0802/0803`锛屼絾鎴愬搧搴撳瓨鏃ュ織缁熶竴鎸?`SG_PURCHASE_IN_STORAGE` 鍐欏叆锛岄渶纭鏄惁涓㈢湡瀹炰笟鍔＄被鍨嬨€?- `effective`锛氭槑缁嗗鏍稿悗缃?`true`锛屽弽瀹″洖閫€ `false`锛屽彲瑙嗕负鈥滆琛屾槸鍚﹀凡鐪熸钀芥垚鍗曚欢鈥濈殑鎵ц鏍囪銆?- `itemId/itemNo/epc`锛氬鏍告椂鎵嶆渶缁堝洖濉埌鍏ュ簱鏄庣粏锛屼笉搴旀妸寤哄崟闃舵鑽夌鍊煎綋鎴愭渶缁堝簱瀛樹富閿€?- `entryBillNum/entryBillWeight`锛氶渶姹傚崟渚х疮璁＄殑鏄湡姝ｅ鏍搁€氳繃鐨勫崟浠朵欢鏁板拰閲戦噸锛屼笉鏄缓鍗曡崏绋挎暟閲忋€?- `maintainBillId/maintainBillDetailId`锛氬崟浠跺叆搴撲細鐩存帴鍙嶅悜椹卞姩缁翠慨鍗曠疮璁★紝涓嶅彧鏄睍绀烘潵婧愬瓧娈点€?- `localId`锛氬崟澶存湭浼犳椂鍚庣浼氳嚜鍔ㄥ彇榛樿搴撲綅锛屾敼浠撳簱鏃惰鍚屾椂妫€鏌ラ粯璁ゅ簱浣嶅彛寰勩€?

## 鍗曚欢璋冩暣鍗曡ˉ鍏呭瓧娈?鐤戠偣
- `processResult`锛氳繖涓ā鍧楀綋鍓嶇湡瀹炴帴 BPM锛宍PROCESS -> APPROVE/REJECT` 浼氬喅瀹氭槸鍚︾湡姝ｆ敼鍗曚欢銆?- `billStatus`锛氭彁浜ゆ祦绋嬪悗浼氳繘鍏?`CONFIRMING`锛屽鎵归€氳繃鎵嶈惤 `CONFIRM`锛屽鎵规嫆缁濅細鍥炲埌 `INIT`銆?- `jsonItem`锛氭槑缁嗛噷浼氬揩鐓ц皟鏁村墠 `ItemDO`锛岃鏄庡悗缁鍑?鍥炴函鍙兘渚濊禆鏃т欢蹇収锛岃€屼笉鍙緷璧栨棩蹇椼€?- `maintainBillDetailId`锛氬嵆浣垮墠绔病浼狅紝瀹℃壒鍥炲啓闃舵涔熶細灏濊瘯鏍规嵁 `itemId` 鍙嶆煡瀛樻椿缁翠慨鏄庣粏銆?- `goodsStockList.num`锛氭垚鍝佸簱瀛樺樊棰濊皟鏁撮噷鍥哄畾浼?`0`锛屽綋鍓嶆洿鍍忊€滄寜閲嶉噺琛ユ墸鈥濊€屼笉鏄€滄寜浠舵暟璋冭处鈥濄€?- `unlockByEpcList`锛氬鎵归€氳繃鍜屽鎵规嫆缁濋兘浼氶噴鏀鹃攣锛岄攣鍙ｅ緞灞炰簬娴佺▼缁撴潫鏃剁粺涓€娓呯悊锛屼笉鏄彧鍦ㄩ€氳繃鍚庨噴鏀俱€?
## 鍗曚欢浣滃簾鍗曡ˉ鍏呭瓧娈?鐤戠偣

- `confirm(Long id)`锛氭柟娉曞悕鍙€滃鏍糕€濓紝浣嗙湡瀹炶涔夋槸鍙戣捣 BPM 骞舵妸鍗曟嵁鎺ㄥ埌 `CONFIRMING`銆?- `itemStatus`锛氬鎵归€氳繃鍚庣敱 `ON_WARE` 鏀规垚 `DISCARD`锛屽弽瀹℃椂鍐嶅洖婊氭垚 `ON_WARE`銆?- `processResult + billStatus`锛氳妯″潡浠嶆槸鍙岀姸鎬佸苟琛岋紝涓嶈兘鍙洴鍗曟嵁鐘舵€佺湅瀹℃壒娴併€?- `PERMIT_CANCEL_BILL_CROSS_DAYS`锛氬崟浠朵綔搴熷崟鍙嶅璺ㄦ棩鏉冮檺甯搁噺銆?- `infirm()`锛氶敊璇枃妗堥噷杩樻畫鐣欌€滀骇鍝佸寘鈥濃€滄牳閿€鐘舵€佲€濆瓧鏍凤紝鏄庢樉鏄覆妯″潡澶嶅埗銆?
## 鍗曚欢鐩樼偣鍗曟搷浣滆ˉ鍏呭瓧娈?鐤戠偣

- `inventory_status`锛歚0=鏈洏鍒癭锛宍1=宸茬洏鍒癭锛屾槸蹇収姹犲唴鐨勭洏鐐圭粨鏋滄爣璇嗐€?- `inventory_source`锛歚0=璐﹂潰蹇収`锛宍1=鐩樼泩琛ュ綍`锛屽喅瀹氭眹鎬诲彛寰勬槸鍚﹀悆杩涘崟澶磋繕鏄粎鍚冩壒娆＄洏鐩堛€?- `inventoryOptType`锛氱洏鐐规柟寮忥紱鎸夊簱浣嶇洏鐐规椂鎵规蹇呴』鎼哄甫 `localId`锛屽苟浼氶檺鍒?EPC 鏇存柊鑼冨洿銆?- `pageInventory()`锛氭€荤洏鐐瑰垪琛ㄥ疄闄呮妸 `wh_item_inventory_bill` 鍜?`wh_inventory_weighing_bill` 鍋氫簡 `UNION`銆?- `approved/returnApproved`锛氬綋鍓嶅彧鏀瑰崟澶寸姸鎬侊紝娌＄湅鍒版寮忓洖鍐欏崟浠舵垨搴撳瓨宸紓鐨勫疄鐜般€?
## 搴撲綅璋冩暣鍗曡ˉ鍏呭瓧娈?鐤戠偣

- `sourLocalId/destLocalId`锛氭槸瀹℃牳鎼簱浣嶅拰鍙嶅鍥炴粴鐨勬牳蹇冮敋鐐广€?- `lockByEpc/unlockByEpcList`锛氳繖涓ā鍧楀缓鍗曞氨閿?EPC锛屼笉鏄鏍告椂鎵嶉攣銆?- `syncId/syncStatus`锛氬簱浣嶈皟鏁村崟鑷甫澶栭儴鍚屾渚ч摼锛屼笉鑳藉彧鎸夊唴閮ㄥ簱瀛樺崟鐞嗚В銆?- `moveStock(...)`锛氳繖閲岃皟鏁寸殑鏄簱浣嶅簱瀛樺垎甯冿紝涓嶆槸鏂板鎴栨墸鍑忓崟浠跺簱瀛樻€婚噺銆?- `processResult/processInstanceId`锛氳〃涓婃湁 BPM 瀛楁锛屼絾褰撳墠瀹炵幇璧扮殑鏄湰鍦板鏍?鍙嶅閾俱€?
## 鍗曚欢鏄庣粏琛ュ厖瀛楁/鐤戠偣

- `itemStatus + lockStatus + wareId + localId`锛氬叡鍚屽喅瀹氬崟浠跺綋鍓嶆槸鍚﹀彲琚悇鎵ц閾剧户缁紩鐢ㄣ€?- `latestBillNo/latestLogType/latestBillType/latestTime`锛氫笉鏄富琛ㄥ瓧娈碉紝鑰屾槸鏌ヨ鏃朵粠 `wh_item_log` 鍥炲～鐨勫睍绀哄彛寰勩€?- `filterCustomerLock`锛氬閿佽鍥鹃€氳繃鈥滄槸鍚﹁繃婊?`customerLock = 0`鈥濇帶鍒讹紝涓嶆槸鍗曠嫭鐨勫瓙琛ㄥ垎椤点€?- `getItemByItemNo()`锛氬疄鐜颁笂鏄?EPC/鏉＄爜娣锋煡锛屾柟娉曞悕浼氳浜鸿浠ヤ负鍙煡鏉＄爜銆?- `itemCheck()`锛氫笉浠呮敼鍗曚欢澶嶆牳瀛楁锛岃繕浼氬弽鎺ㄥ崟浠跺叆搴撳崟鏄庣粏鍜屽崟澶村鏍哥姸鎬併€?
## 鐩樼偣绉伴噸鍗曡ˉ鍏呭瓧娈?鐤戠偣

- `isGen`锛氱郴缁熻嚜鍔ㄨˉ榻愮殑鍩虹嚎鏄庣粏鏍囪锛岃鎯呴〉榛樿浼氳繃婊ゅ畠銆?- `profitLossNum/profitLossGoldWeight`锛氬鏍稿悗鎵嶆湁鍊硷紝琛ㄧず璐﹀疄宸紝涓嶄唬琛ㄦ寮忓簱瀛樺凡璋冩暣銆?- `inventoryType`锛氭湀鐩?鏃ョ洏瀛楁锛屼粎琛ㄨ揪鐩樼偣绫诲瀷锛屼笉鍐冲畾搴撳瓨杩囪处鏂瑰悜銆?- `3701/3702`锛氬叡琛ㄥ叡鏈嶅姟锛屼絾鎴愬搧绉伴噸渚濊禆 `wh_sku_stock`锛屽師鏂欑О閲嶄緷璧?`wh_material_stock`銆?- `pageInventory`锛氱洏鐐规€诲垪琛ㄩ噷瀹冨拰鍗曚欢鐩樼偣鍗曟槸鍚屽煙鍏辩敤鍏ュ彛銆?

## 缁翠慨鍗曡ˉ鍏呭瓧娈?鐤戠偣
- `wh_maintain_bill.billStatus`锛氬崟澶村鏍告€侊紝鍙〃绀烘湭瀹?宸插/鍙嶅/浣滃簾锛屼笉浠ｈ〃缁翠慨涓氬姟鏄惁瀹岀粨銆?- `wh_maintain_bill.replyStatus`锛氱淮淇繘搴﹁仛鍚堟€侊紝鍙楃淮淇叆搴撱€佺粨绠椼€佷笂鏌滅瓑绱瀛楁鍏卞悓椹卞姩銆?- `wh_maintain_bill.transferStatus`锛氭槸鍚﹀凡鎵ц涓€閿皟鎷紝鏄嫭绔嬩簬瀹℃牳鎬佸拰缁翠慨鎬佺殑绗笁鏉＄姸鎬佺嚎銆?- `wh_maintain_bill.detailType`锛歚1=鍗曚欢缁翠慨锛?=鎴愬搧/SKU缁翠慨`锛屽喅瀹氬悗缁寕鍗曚欢杩樻槸璧版垚鍝佸簱瀛樺垎鏀€?- `wh_maintain_bill_detail.toCounter`锛氬彧鏄€滆缃埌鏌溾€濇祦绋嬫爣璁帮紝涓嶇瓑浜庢寮忓簱瀛樺姩浣溿€?- `wh_maintain_bill_detail.replyStatus`锛氭槑缁嗙骇缁翠慨杩涘害鎬侊紝鏈€缁堜細鍚屾鍒板崟浠?`maintainBillDetailReplyStatus`銆?- `wh_maintain_bill.totalFinishNum/totalInboundNum/totalSettleNum/totalCounterNum`锛氳繖浜涚疮璁″瓧娈靛叡鍚屽喅瀹氬崟澶?`replyStatus`锛屼笉鏄函灞曠ず瀛楁銆?

## 缁翠慨鍏ュ簱鍗曡ˉ鍏呭瓧娈?鐤戠偣
- `wh_maintain_in_bill.bizType`锛歚9001=鍗曚欢缁翠慨鍏ュ簱锛?002=鎴愬搧缁翠慨鍏ュ簱`锛岀敱鏄庣粏鏄惁瀛樺湪 `itemId` 鑷姩鍐冲畾銆?- `wh_maintain_in_bill.totalMaintainCmtFee`锛氱淮淇伐璐瑰悎璁★紝鎸夌淮淇厠宸ヨ垂/浠跺伐璐归噸绠椼€?- `wh_maintain_in_bill.totalGeneralCmtFee`锛氭櫘閫氬伐璐瑰悎璁★紝鎸夊師鎴愭湰鍏嬪伐璐?浠跺伐璐归噸绠椼€?- `wh_maintain_in_bill.totalPushCount`锛氬凡涓嬫帹缁翠慨鍑哄簱鍗曟嵁鏁伴噺锛屽彧瑕佸ぇ浜?0锛屽弽瀹″氨浼氳绂佹銆?- `wh_maintain_in_bill_detail.maintainOutBillDetailId`锛氱淮淇嚭搴撴槑缁嗘潵婧愰敋鐐癸紝涔熸槸寰呭叆搴撻噺鏍￠獙鐨勪緷鎹€?- `wh_maintain_in_bill_detail.originGoldWeight/originGrossWeight/originWeight`锛氬崟浠跺弽瀹℃椂鎭㈠缁翠慨鍓嶇姸鎬佺殑鍩虹嚎鍊笺€?- `wh_maintain_in_bill_detail.maintainCmtFee/generalCmtFee/cmtFee`锛氬垎鍒〃绀虹淮淇伐璐广€佹櫘閫氬伐璐瑰拰鎬诲伐璐癸紝閮戒細鍙備笌渚涘簲鍟嗛挶鍖呭彛寰勩€?

## 缁翠慨鍑哄簱鍗曡ˉ鍏呭瓧娈?鐤戠偣
- `wh_maintain_out_bill.bizType`锛歚8001=鍗曚欢缁翠慨鍑哄簱锛?002=鎴愬搧缁翠慨鍑哄簱`锛岀敱鏄庣粏鏄惁瀛樺湪 `itemId` 鑷姩鍐冲畾銆?- `wh_maintain_out_bill.totalSettleWeight/totalSettleMoney`锛氭湰鍗曠粨绠楅噸閲?閲戦鍙ｅ緞锛屼細杩涘叆渚涘簲鍟嗛挶鍖呮墸鍑忛摼銆?- `wh_maintain_out_bill.totalInNum`锛氬凡琚淮淇叆搴撳洖鏀剁殑鏁伴噺锛屽彧瑕佸ぇ浜?0锛屽弽瀹″氨浼氳绂佹銆?- `wh_maintain_out_bill.totalPushCount`锛氬凡琚笅鎺ㄥ埌鍚庣画鍗曟嵁鐨勬暟閲忥紝鍙澶т簬 0锛屼篃浼氶樆鏂弽瀹°€?- `wh_maintain_out_bill_detail.inboundNumBak/repareFeeBak/newItemIdBak/...`锛氬鏍告椂澶囦唤缁翠慨鍗曟槑缁嗕笂涓€杞叆搴撶粨鏋滐紝鍙嶅闈犺繖缁勫瓧娈垫仮澶嶃€?- `wh_maintain_out_bill_detail.recordDetailId`锛氶€€鍘傜櫥璁版槑缁嗘潵婧愰敋鐐癸紝鍐冲畾鐧昏姹?`maintainOutNum` 鐨勭疮璁″彛寰勩€?- `wh_maintain_out_bill_detail.pushMaintainInBillDetailId`锛氳缁翠慨鍏ュ簱鑽夌涓嬫帹鍚庣殑鎸傛帴鐥曡抗锛屼篃鏄綔搴?鍙嶅闄愬埗鐨勯噸瑕佷緷鎹€?

## 缁翠慨鍗曡鎯呰ˉ鍏呭瓧娈?鐤戠偣
- `replyStatus`锛氱淮淇槑缁嗙湡瀹炶繘搴︿富瀛楁锛涚淮淇鎯呮ā鍧楁病鏈夎嚜宸辩殑瀹℃牳娴侊紝鏍稿績閮芥矇鍦ㄨ繖鏉＄姸鎬佺嚎涓娿€?- `finalizationReason/finalizationTime/finalizationMan`锛氬彧鍦?`finalization(...)` 缁堢粨鍔ㄤ綔閲岀粺涓€鍥炲啓锛屽睘浜庣淮淇煙鐨勭粓缁撳厓鏁版嵁銆?- `outNum/inNum`锛氬湪缁翠慨璇︽儏缁堢粨鏃朵細琚洿鎺ヨˉ鍒拌嚦灏戠瓑浜?`num`锛岃涔変笉鍐嶆槸鈥滅湡瀹炴祦姘寸疮璁♀€濓紝鑰屾洿鎺ヨ繎鈥滅粓缁撳悗瑙嗕负闂幆鏁伴噺鈥濄€?- `stepWare/stepLocal`锛氱淮淇祦杞腑鐨勫綋鍓嶄粨浣嶏紱`updateStepLocal(...)` 杩樹細鍦ㄤ粨搴撳懡涓枃妗?`"缂栬揣浠?` 鏃堕『鎵嬫妸 `replyStatus` 鎺ㄥ埌 `"6"`銆?- 鈥滄渶杩戜竴娆＄淮淇叆搴撶粨鏋溾€濓細缁翠慨璇︽儏鏌ヨ銆佸崟浠跺叆搴撴潵婧愩€佸崟浠惰皟鏁存潵婧愰兘浼氫緷璧?`MaintainInBillDetailMapper.selectLastByMaintainIds/findLastByMaintainBillDetailIds`锛屾槸缁翠慨鍚庣粨鏋滃睍绀轰笌涓嬫帹鐨勫叡鍚屽彛寰勩€?

## 缁翠慨鍗曟姤琛ㄨˉ鍏呭瓧娈?鐤戠偣
- `out_* / in_*`锛氭姤琛ㄩ噷鐨勯€佸巶/鍥炲巶鎸囨爣涓嶆槸鏉ユ簮浜庣淮淇崟澶达紝鑰屾槸鍒嗗埆浠庣淮淇嚭搴撳崟鍜岀淮淇叆搴撳崟鑱氬悎鍚庡啀骞惰〃瀵圭収銆?- `replyType`锛氭姤琛ㄤ笓鐢ㄧ瓫閫夊彛寰勶紱`宸插洖/鏈洖/鍦ㄥ巶` 涓嶆槸鍗曡〃鍘熷鐘舵€侊紝鑰屾槸鎸夌疮璁￠噺鍜屼粨浣嶆帹瀵煎嚭鐨勪簩娆＄姸鎬併€?- `finalizationReason/finalizationTime/finalizationMan`锛氭槑缁嗘姤琛ㄧ洿鎺ヤ粠 `wh_maintain_bill_detail` 璇荤粓缁撲俊鎭紝骞跺彲瑕嗙洊灞曠ず鐘舵€佷负鈥滅粓缁撯€濄€?- `maintainbillsettle`锛氬悕瀛楃湅璧锋潵鍍忔姤琛紝浣嗗疄闄呬笂鍙槸缁翠慨缁撶畻鏄庣粏 CRUD/瀵煎嚭妯″潡锛屼笉鎵挎媴缁翠慨 BI 姹囨€昏亴璐ｃ€?

## 濮斿鍔犲伐璁㈠崟琛ュ厖瀛楁/鐤戠偣
| 瀛楁 | 鎵€鍦ㄨ〃 | 璇箟 | 澶囨敞 |
|------|--------|------|------|
| `orderStatus` | `wh_outsourcing_order` | 濮斿涓荤姸鎬?| `CONFIRM/CANCELLATION/DELIVERED/FINISH` 涓嶆槸绾挎€ф帹杩涳紝鑰屾槸鎸夋槑缁嗙疮璁￠噸绠?|
| `receiveStatus` | `wh_outsourcing_order` | 渚涘簲鍟嗘帴鏀剁姸鎬?| 瀹℃牳鏃舵牴鎹?`checkBom(...)` 鍐冲畾鍐?`UN_RECEIVE` 杩樻槸 `NONE` |
| `totalDeliveryNum` | `wh_outsourcing_order` | 绱鏍搁攢閲?| 鐢辫鍗曟牳閿€閾捐仛鍚堬紝涓嶆槸濮斿鍗曡嚜宸卞～鍐?|
| `totalFinishNum` | `wh_outsourcing_order` | 绱缁堟鏍搁攢閲?| 鐢?`finish(...)` 鑱氬悎 |
| `sumGramWeight` | `wh_outsourcing_order_detail` | 棰勮鎬婚噸 | 褰撳墠浠ｇ爜鎸夊钩鍧囧厠閲?* `demandNum` 璁＄畻锛屼笉鏄?* `outsourcingNum` |
| `convertInboundNo` | `wh_outsourcing_order_detail` | 杞垚鍝佸叆搴撳崰鐢ㄦ爣璁?| 闈炵┖鍚庝細琚?`inboundOptions(...)` 鎺掗櫎 |
| `factoryAnswer` | `wh_outsourcing_order_detail` | 宸ュ巶鍥炲鏂囨湰 | 鍙洖鍐欐枃鏈紝涓嶉┍鍔ㄧ姸鎬佹祦 |
| `processResult` | `wh_outsourcing_order` | BPM 瀹℃壒缁撴灉 | 褰撳墠鍙鐥曡抗鍥炲啓锛屾湭瑙佷笟鍔＄姸鎬佽仈鍔?|

## 缁翠慨缁撶畻鏄庣粏琛ュ厖瀛楁/鐤戠偣
| 瀛楁 | 鎵€鍦ㄨ〃 | 璇箟 | 澶囨敞 |
|------|--------|------|------|
| `settleNo` | `wh_maintain_bill_settle` | 缁撶畻缂栫爜 | 鍦ㄧ淮淇‘璁ら摼閲屽喅瀹氭槸鍚﹁繘鍏?`CLOSING_PRICE` 缁撲环鍐插洖鍒嗘敮 |
| `materialNo` | `wh_maintain_bill_settle` | 閽卞寘鏉愯川缁村害 | 涓虹┖鏃朵笉浼氳繘鍏ユ潗璐ㄩ挶鍖呰仛鍚?|
| `settleClass` | `wh_maintain_bill_settle` | 閽卞寘鍒嗙被缁村害 | 涓?`materialNo` 缁勬垚鑱氬悎閿?|
| `settleWeight` | `wh_maintain_bill_settle` | 缁撶畻閲嶉噺 | 琚洿鎺ュ綋鎴愭潗璐ㄩ挶鍖呬綑棰濆彉鍔ㄩ噺 |
| `settleMoney` | `wh_maintain_bill_settle` | 缁撶畻閲戦 | 琚眹鎬绘垚缁翠慨纭閾剧幇閲戦挶鍖呴噾棰?|
| `settleList` | `MaintainBillStoreVO` | 缁翠慨鍗曡〃鍗曠粨绠楄 | 褰撳墠涓绘湇鍔℃湭鏄庢樉鐪嬪埌淇濆瓨涓庡洖鏄炬帴绾?|

## 濮斿鍔犲伐璁㈠崟鏄庣粏琛ュ厖瀛楁/鐤戠偣
| 瀛楁 | 鎵€鍦ㄨ〃 | 璇箟 | 澶囨敞 |
|------|--------|------|------|
| `totalWriteoffNum` | `wh_outsourcing_order_detail` | 绱鏍搁攢閲?| 寰呭崟浠跺叆搴撴睜鍜岄噰璐眹鎬婚兘鐩存帴渚濊禆瀹?|
| `finishNum` | `wh_outsourcing_order_detail` | 绱缁堟閲?| 閲囪喘姹囨€荤殑鏈洖閲忎細鎵ｆ帀瀹?|
| `convertInboundNo` | `wh_outsourcing_order_detail` | 杞垚鍝佸叆搴撳崰鐢ㄦ爣璁?| 闈炵┖鍗充粠杞垚鍝佸叆搴撴潵婧愭睜鎺掗櫎 |
| `canEntryNum/canEntryGramWeight` | 鏌ヨ缁撴灉瀛楁 | 灏氬彲杞崟浠跺叆搴撻搴?| 鐢辨牳閿€閲忓噺鍘诲凡鍏ュ簱閲忓姩鎬佽绠?|
| `totalNotReturnNum` | 鏌ヨ/瀵煎嚭绛涢€夊彛寰?| 鏈洖浠舵暟 | 鍒嗛〉銆佸鍑哄拰姹囨€讳笁澶勫彛寰勮涓€璧风湅 |
| `factoryAnswer` | `wh_outsourcing_order_detail` | 宸ュ巶鍥炲 | 宸ュ巶瀵煎嚭鐩存帴灞曠ず |

## 缁撶畻鏂瑰紡琛ュ厖瀛楁/鐤戠偣
| 瀛楁 | 鎵€鍦ㄨ〃 | 璇箟 | 澶囨敞 |
|------|--------|------|------|
| `settleNo` | `wh_settlement` | 缁撶畻缂栫爜 | 涓氬姟渚у緢澶氳鍒欐寜缂栫爜纭瘑鍒?|
| `settleMethod` | `wh_settlement` | 缁撶畻鍚嶇О | 閮ㄥ垎鏃ч€昏緫浠嶆寜鍚嶇О璇嗗埆锛屽瓨鍦ㄥ彛寰勫垎鍙?|
| `settleType` | `wh_settlement` | 缁撶畻绫诲瀷 | 娉ㄩ噴鍙杞瑺/缁撲环/宸ヨ垂/璐圭敤 |
| `bizType` | `wh_settlement` | 浜嬪姟绫诲瀷 | 鍚岀紪鐮佸湪涓嶅悓浜嬪姟绫诲瀷涓嬪彲鍒嗗埆瀹氫箟 |
| `settleClass` | `wh_settlement` | 缁撶畻绫诲埆 | 琚悇涓氬姟缁撶畻鏄庣粏缁ф壙浣跨敤 |

## 浠撳簱缁撴瀯琛ュ厖瀛楁/鐤戠偣
| 瀛楁 | 鎵€鍦ㄨ〃 | 璇箟 | 澶囨敞 |
|------|--------|------|------|
| `bigClassIds/textureIds` | `wh_warehouse` | 浠撳簱鍙敤澶х被/鏉愯川鑼冨洿 | 涓氬姟寤哄崟鏃跺父浣滀负鍙€夎寖鍥存潵婧?|
| `areaNo/areaName` | `wh_warehouse_area` | 搴撳尯缂栧彿/鍚嶇О | 褰撳墠鍒涘缓/鏇存柊鏃跺熀鏈己鍒?`areaName = areaNo` |
| `localNo/localName` | `wh_warehouse_location` | 搴撲綅缂栧彿/鍚嶇О | 褰撳墠鍒涘缓/鏇存柊鏃跺己鍒?`localName = localNo` |
| `stockShare` | `wh_warehouse_location` | 鏄惁鍏变韩搴撲綅 | 鍏变韩搴撳瓨妯″潡鐩存帴渚濊禆 |
| 榛樿搴撳尯 | `A` | 榛樿搴撳尯绾﹀畾 | 寤轰粨涓庡厹搴曢€昏緫閮戒緷璧栬繖涓害瀹?|
| 榛樿搴撲綅 | `A0/A01` | 榛樿搴撲綅绾﹀畾 | 褰撳墠浠ｇ爜鍙ｅ緞涓嶄竴鑷达紝闇€閲嶇偣纭 |

## 瀹㈡埛妗ｆ琛ュ厖璇箟
- `status`锛氬鎴峰惎鍋滅姸鎬侊紝涓嶇瓑浜庡鎵圭姸鎬侊紱椤剁骇瀹㈡埛棣栨寤烘。榛樿 `DISABLE`锛屽鎵归€氳繃鍚庢墠浼氬垏鍒?`ENABLE`銆?- `processResult`锛氬鎴峰鎵圭姸鎬侊紝椤剁骇瀹㈡埛浼氱粡鍘?`UN_COMMIT -> PROCESS -> APPROVE/REJECT`锛涘瓙瀹㈡埛瀵煎叆鏃剁洿鎺ュ啓鎴?`APPROVE`銆?- `approvedTime`锛氭槸鍚︽浘鐪熸瀹℃壒鐢熸晥鐨勯噸瑕佸垎鐣岋紱宸插鎵瑰鎴峰悗缁慨鏀逛細杩涘叆鈥滅紦瀛樺緟瀹℃壒鈥濇ā寮忋€?- `parentId/level`锛氬鎴峰眰绾ц涔夛紱`level = 1` 鏄《绾у鎴凤紝`level = 2` 鏄瓙瀹㈡埛锛屽緢澶氬鎵?璐︽埛閫昏緫鍙椤剁骇瀹㈡埛鐢熸晥銆?- `accountId`锛氶《绾у鎴疯储鍔¤处鎴风粦瀹氾紱涓嶆槸鏅€氬閿紝瀹℃壒銆佸垹闄ゃ€佸悕绉板悓姝ャ€佸巻鍙茶ˉ璐﹂兘渚濊禆瀹冦€?- `mallAccount`锛氬晢鍩庤处鍙凤紝涓嶇瓑浜庤储鍔¤处鎴风紪鐮侊紝鍙兘鍦ㄥ鎴峰凡鍚敤鏃剁敓鎴愩€?

## 渚涘簲鍟嗘。妗堣ˉ鍏呰涔?- `supplierNo`锛氫緵搴斿晢涓荤紪鍙凤紝鍒涘缓鍜屽鍏ラ兘浠ュ畠涓烘牳蹇冭瘑鍒敭涔嬩竴銆?- `supplierCode`锛氫緵搴斿晢浠ｇ爜锛涘鍏ヨ姹傞潪绌烘椂蹇呴』鏄?2 浣嶆暟瀛楋紝涓嶇瓑浜?`supplierNo`銆?- `accountId`锛氫緵搴斿晢璐㈠姟璐︽埛缁戝畾锛涘垱寤恒€佹洿鏂般€佸鍏ャ€佸垹闄や繚鎶ら兘鍥寸粫瀹冨睍寮€銆?- `status`锛氫緵搴斿晢鍚仠鐘舵€侊紝涓嶅甫瀹℃壒璇箟锛涘綋鍓嶅彧鏀逛富妗ｏ紝涓嶈仈鍔ㄨ处鎴锋垨閽卞寘銆?- `supplierType`锛氬鍏ュ繀濉瓧娈碉紝鏄緵搴斿晢涓绘。鍒嗙被缁村害涔嬩竴銆?

## 鏉愯川妗ｆ琛ュ厖璇箟
- `textureNo`锛氭潗璐ㄧ紪鍙凤紱褰撳墠浠ｇ爜瑕佹眰鈥滄暟瀛楁牸寮忊€濓紝浣嗗疄鐜板厑璁歌礋鏁板拰灏忔暟銆?- `walletTypeNo`锛氭潗璐ㄥ搴旂殑閽卞寘绫诲瀷缂栫爜锛屾槸鏉愯川閽卞寘鏄犲皠鐨勫叧閿瓧娈点€?- `walletGoodsType`锛氶挶鍖呯墿鍝佺被鍨嬶紝鍐冲畾鏉愯川钀藉摢绫婚挶鍖呭彛寰勩€?- `convertRate`锛氭潗璐ㄦ姌绠楀惈閲忥紝鐢ㄤ簬鏉愯川鎹㈢畻璇箟銆?- `semisClassNo/semisClassName`锛氭潗璐ㄦ寕鎺ョ殑鍗婃垚鍝佸ぇ绫讳俊鎭紝涓嶅彧鏄睍绀哄瓧娈点€?

## 澶х被妗ｆ琛ュ厖璇箟
- `bigClassNo`锛氬ぇ绫荤紪鍙凤紱褰撳墠寤烘。鏄惧紡鎸夊畠鍋氬敮涓€鏍￠獙銆?- `bigClassName`锛氬ぇ绫诲悕绉帮紱甯歌鍚嶇О鏄犲皠鎺ュ彛涓庡鍏ュ彛寰勪娇鐢ㄣ€?- `bigType`锛氬ぇ鍒嗙被璇箟锛屼笖浼氬洖鍐欏埌 SKU 鍐椾綑瀛楁銆?- `salePriceMode/storePriceMode`锛氶攢鍞浠锋柟寮忎笌瀛樿揣璁′环鏂瑰紡锛屼笉鏄函灞曠ず瀛楁銆?- `goodsType/goodsClassify`锛氱墿璧勭被鍨嬩笌鐗╄祫鍒嗙被锛屾槸鍟嗗搧缁撴瀯灞傝鍒欏瓧娈点€?

## 鍝佺被缁撴瀯琛ュ厖璇箟
- `categoryNo`锛氫竴绾у搧绫荤紪鍙凤紱褰撳墠寤烘。鏄惧紡鎸夊畠鍋氬敮涓€鏍￠獙銆?- `subCategoryNo`锛氬瓙鍝佺被缂栧彿锛涘瓙鍝佺被寤烘。鍜屾敼妗ｉ兘鍥寸粫瀹冨仛鍞竴绾︽潫銆?- `categoryId`锛氬瓙鍝佺被鎵€灞炵埗鍝佺被 ID锛屼篃鏄晢鍩庡垎绫绘爲瀛愯妭鐐圭殑 `parentId`銆?- `status`锛氬搧绫?瀛愬搧绫诲惎鍋滅姸鎬侊紱浣嗗瓙鍝佺被閮ㄥ垎鎺ュ彛褰撳墠骞舵湭绋冲畾鎸夊畠杩囨护銆?

## 浜у搧璧勬枡琛ュ厖璇箟
- `productNo`锛氫骇鍝佺紪鍙凤紱褰撳墠寤烘。鍞竴鎬т富瑕佸洿缁曞畠灞曞紑銆?- `categoryId`锛氫骇鍝佹墍灞炲搧绫?ID锛涗絾瀵煎叆 Excel 鍚屽悕瀛楁瀹為檯浼犵殑鏄€滃搧绫荤紪鍙封€濄€?- `textureId`锛氫骇鍝侀粯璁ゆ潗璐?ID锛屽睍绀哄眰浼氬洖濉?`textureName`銆?- `productType`锛氫骇鍝佺被鍨嬪瓧鍏稿€硷紱瀵煎叆鏃朵細閫氳繃 `product_type` 鍋氳浆鎹€?- `storageType`锛氬簱瀛樼被鏍囪锛涘鍏ラ€昏緫閲岃嚦灏戞妸鈥滃簱瀛樼被鈥濇槧灏勬垚 `0`銆?- `fixedPrice/moreCmtFee`锛氫骇鍝侀粯璁や环鏍间笌闄勫姞宸ヨ垂瀛楁锛屾槸鍟嗗搧涓绘。涓婄殑璁′环琛ュ厖淇℃伅銆?

## 娆惧紡搴撹ˉ鍏呰涔?- `skuStatus`锛氭寮忓惎鍋滅姸鎬侊紱鏂板缓榛樿 `DISABLE`锛屼笉绛変簬瀹℃壒鐘舵€併€?- `processResult/processUserId/processInstanceId/approvedTime`锛歋KU 瀹℃壒閾惧瓧娈碉紝璇存槑瀹冧笉鏄函涓绘。鑰屾槸瀹℃壒涓绘。銆?- `bigClassNo/textureNo/categoryNo`锛氭潵鑷富鏁版嵁鐨勫啑浣欑紪鐮佸瓧娈碉紝涓嶅彧鏄睍绀哄睘鎬с€?- `supplierId/supplierCode/supplierName`锛氬綋鍓嶉粯璁や緵搴斿晢鎶曞奖缁撴灉锛屼笉绛変簬瀹屾暣 BOM 鍒楄〃銆?- `costGramCmtFee/costItemCmtFee/...`锛氶粯璁や緵搴斿晢 BOM 鎶曞奖鍒?SKU 涓昏〃鐨勮垂鐢ㄥ彛寰勩€?

| 渚涘簲鍟嗘寮?/ BOM | `isDirect` | 渚涘簲鍟?BOM 澶磋〃 | 榛樿渚涘簲鍟嗘爣璁?| `sku` 寤烘/鏀规鏃剁敤瀹冨喅瀹氬摢鏉?BOM 璐圭敤鎶曞奖鍥?`wh_sku` |
| 渚涘簲鍟嗘寮?/ BOM | `bomSerialNo` | 渚涘簲鍟?BOM 澶磋〃 | BOM 缁勯敋鐐?| 楗扮煶/杈呮枡鏌ヨ閾句細鎸夊畠閲嶆柊鍒嗙粍锛屼笉鍙槸灞曠ず娴佹按鍙?|
| 渚涘簲鍟嗘寮?/ BOM | `skuSupplierId` | accessories / part 鍗槦琛?| 褰掑睘澶磋〃 ID | 鍗槦鏄庣粏閫氳繃瀹冩寕鍦?`wh_supplier_sku` 涔嬩笅 |
| 渚涘簲鍟嗘寮?/ BOM | `costGramCmtFee` 绛夎垂鐢ㄥ瓧娈?| 渚涘簲鍟?BOM 澶磋〃 | 榛樿璐圭敤鏉ユ簮 | 榛樿渚涘簲鍟嗚閲岀殑璐圭敤浼氭暣浣撴姇褰卞埌 `wh_sku` 涓昏〃 |

| SKU瑙勬牸 | `circleMouth` | 瑙勬牸琛?| 鍦堝彛/闀垮害瑙勬牸 | 瑙勬牸灞傜殑涓诲睍绀哄瓧娈典箣涓€ |
| SKU瑙勬牸 | `specSize` | 瑙勬牸琛?| 灏哄 | 鍜?`circleMouth` 鍏卞悓鎻忚堪 SKU 瑙勬牸 |
| SKU瑙勬牸 | `minWeight/maxWeight` | 瑙勬牸琛?| 鍐呴儴閲嶉噺涓婁笅闄?| 灞炰簬 SKU 鐢熸晥瑙勬牸鍙ｅ緞 |
| SKU瑙勬牸 | `customerMinWeight/customerMaxWeight` | 瑙勬牸琛?| 瀹㈡埛閲嶉噺涓婁笅闄?| 灞炰簬瀹㈡埛瑙嗚涓嬬殑瀹氬埗瑙勬牸鍙ｅ緞 |
| SKU瑙勬牸 | `isDirect` | 瑙勬牸琛?| 鐩撮噰/鐗规畩瑙勬牸鏍囪 | 涓庝緵搴斿晢 BOM 灞傚悓鍚嶄絾涓嶅悓涔夛紝涓嶈兘娣风湅 |

| SKU瀹℃壒鍩?| `latestOperateBatch` | 鏂板缓娆?鏀规鏄庣粏 | 瀹℃壒鎵规鑱氬悎閿?| 浼佸井寰呭姙鍜屽鎵规棩蹇楅兘浼氭寜瀹冭仛鍚堝悓鎵规搷浣?|
| SKU瀹℃壒鍩?| `state` | 鏂板缓娆?鏀规鏄庣粏 | 褰撳墠瀹℃壒闃舵 | 妯℃澘銆佸緟鍔炪€佸彲瑙佹潈闄愬拰闃舵鏂囨湰閮戒緷璧栧畠 |
| SKU瀹℃壒鍩?| `startState` | 鏀规鏄庣粏 | 闃舵璧风偣 | 鏀规浼佸井寰呭姙浼氬悓鏃跺睍绀哄綋鍓嶉樁娈靛拰璧峰闃舵 |
| SKU瀹℃壒鍩?| `skuDetailId` | 鏂板缓娆?鏀规缂撳瓨鍗槦琛?| 缂撳瓨鏄庣粏褰掑睘閿?| 瑙勬牸銆佷緵搴斿晢銆佽緟鐭炽€侀厤浠堕兘閫氳繃瀹冩寕鍒板鎵规槑缁?|

| SKU瀹℃壒鏄庣粏 | `latestOperateBatch` | detail 瀹夸富 | 鎵规鑱氬悎閿?| 鍒楄〃銆佷紒寰€佹棩蹇楀洖鏀鹃兘浼氭寜瀹冭仛鍚堝悓鎵规搷浣?|
| SKU瀹℃壒鏄庣粏 | `state` | detail 瀹夸富 | 褰撳墠瀹℃壒闃舵 | 瀹夸富琛ㄤ笂鐨勪富鎵ц鎬?|
| SKU瀹℃壒鏄庣粏 | `startState` | 鏀规 detail 瀹夸富 | 鏀规璧峰闃舵 | 鐢ㄤ簬鎸夋敼娆剧被鍨嬪拰鏈悓姝ユ睜瑙ｉ噴鏈鏀规 |
| SKU瀹℃壒鏃ュ織 | `operateBatch` | log 杞ㄨ抗琛?| 鎿嶄綔鎵规閿?| 鍜?detail 鐨勬壒娆￠敭鍏卞悓缁勬垚瀹℃壒鍘嗗彶瑙嗗浘 |
| SKU瀹℃壒鏃ュ織 | `operateType` | log 杞ㄨ抗琛?| 瀹℃壒鍔ㄤ綔绫诲瀷 | 鐢ㄦ潵鍖哄垎鎻愪氦銆侀€氳繃銆侀┏鍥炪€佽浆鍔炵瓑杞ㄨ抗 |

| SKU瀹℃壒BOM缂撳瓨 | `skuDetailId` | 瀹℃壒鍩?supplier / accessories / part 缂撳瓨 | 瀹℃壒鏄庣粏褰掑睘閿?| 瀹℃壒鍩熼噷鐪熸鐨勪竴灞傚綊灞炰富閿?|
| SKU瀹℃壒BOM缂撳瓨 | `skuSupplierId` | 瀹℃壒鍩?accessories / part 缂撳瓨 | 缂撳瓨 supplier 澶村綊灞為敭 | 杈呯煶鍜岄厤浠堕兘鎸傚湪 supplier 缂撳瓨澶翠箣涓?|
| SKU瀹℃壒BOM缂撳瓨 | `isDirect` | 瀹℃壒鍩?supplier 缂撳瓨 | 榛樿渚涘簲鍟嗘爣璁?| 瀹℃壒鏈熼棿灏辩敤浜庣‘瀹氶粯璁や緵搴斿晢 |
| SKU瀹℃壒BOM缂撳瓨 | `bomSerialNo` | 瀹℃壒鍩?supplier 缂撳瓨 | BOM 缁勫簭鍒楅敋鐐?| 寰堝彲鑳藉湪姝ｅ紡 BOM 鐢熸晥鏃舵部鐢?|

| SKU瀹℃壒杈呭姪閾?| `errorMessage` | Analysis BO | 瀵煎叆闃绘柇閿欒闆嗗悎 | 鏈夐敊璇椂鏁存壒瀵煎叆鐩存帴涓 |
| SKU瀹℃壒杈呭姪閾?| `warnMessage` | 鏀规 Analysis BO | 瀵煎叆璀﹀憡闆嗗悎 | 鏀规瀵煎叆浼氬尯鍒嗚鍛婂拰閿欒 |
| SKU瀹℃壒杈呭姪閾?| `latestOperateBatch` | 鍚屾/鍥為€€閾?| 鏈疆鍚屾鎴栧洖閫€鎵规鍙?| 鐢熸晥涓庡洖閫€閮戒細鐢熸垚鏂版壒娆″彿 |
| SKU瀹℃壒杈呭姪閾?| `syncTime` | detail 瀹夸富 | 姝ｅ紡鐢熸晥鏃堕棿 | 鏂板缓娆?鏀规鍚屾瀹屾垚鍚庨兘浼氬洖鍐?|

| 璁捐鍏冪礌 | `elementBigType` | 璁捐鍏冪礌涓绘。 | 鍏冪礌澶х被 | 鍐冲畾瀛愮被鏉ユ簮鏄搧绫昏繕鏄摢濂楀瓧鍏?|
| 璁捐鍏冪礌 | `elementSubType` | 璁捐鍏冪礌涓绘。 | 鍏冪礌瀛愮被 | 璁捐鍏冪礌 1 璧板搧绫伙紝2/3/4 璧板瓧鍏稿瓙绫?|
| 璁捐鍏冪礌 | `designNo` | 璁捐鍏冪礌涓绘。 | 鍏冪礌缂栫爜 | SKU 瀹℃壒鍩熷拰璇︽儏椤靛父鎸夌紪鐮佸洖鏌ュ厓绱?|
| 鐢熶骇宸ヨ壓 | `processNo` | 鐢熶骇宸ヨ壓涓绘。 | 宸ヨ壓缂栫爜 | 瀹℃壒鍩熷鍏ュ拰鎵归噺淇敼鎸夊畠鍥炴煡宸ヨ壓 |
| 鐢熶骇宸ヨ壓 | `processName` | 鐢熶骇宸ヨ壓涓绘。 | 宸ヨ壓鍚嶇О | 鏈€缁堜細琚牸寮忓寲鍥炲啓鍒?SKU / 鏀规璇︽儏灞曠ず |

| 绯诲垪鏄犲皠 | `seriesNo` | 绯诲垪涓绘。 | 绯诲垪缂栫爜 | 杞婚噺瑙勫垯涓绘暟鎹富閿箣涓€ |
| 绯诲垪鏄犲皠 | `seriesId` | 绯诲垪鍏崇郴琛?| 绯诲垪褰掑睘閿?| 鎶?SKU 鎸傚埌鏌愪釜绯诲垪涓?|
| 濂楃郴鏄犲皠 | `setNo` | 濂楃郴瀹夸富 / SKU 涓绘。 | 濂楃郴缂栫爜 | 浼氬弽鍐欏埌 `wh_sku` |
| 濂楃郴鏄犲皠 | `setName` | 濂楃郴瀹夸富 / SKU 涓绘。 | 濂楃郴鍚嶇О | 浼氬弽鍐欏埌 `wh_sku` |
| 濂楃郴鏄犲皠 | `skuId1...skuId6` | 濂楃郴瀹夸富 | 濂楃郴鎴愬憳妲戒綅 | 褰撳墠鍥哄畾鏈€澶?6 涓?SKU 鎴愬憳 |

| SKU缂栫爜瑙勫垯 | `categoryCode` | 瑙勫垯涓昏〃 | 鍝佺被浠ｇ爜缁村害 | SKU 搴忓垪姹犵殑绗竴灞傛媶鍒嗙淮搴?|
| SKU缂栫爜瑙勫垯 | `supplierCode` | 瑙勫垯涓昏〃 | 渚涘簲鍟嗕唬鐮佺淮搴?| SKU 搴忓垪姹犵殑绗簩灞傛媶鍒嗙淮搴?|
| SKU缂栫爜瑙勫垯 | `saleFeeCode` | 瑙勫垯涓昏〃 | 閿€鍞伐璐逛唬鐮佺淮搴?| SKU 搴忓垪姹犵殑绗笁灞傛媶鍒嗙淮搴?|
| SKU缂栫爜瑙勫垯 | `serialNo` | 瑙勫垯涓昏〃/鏃ュ織琛?| 褰撳墠鎴栧巻鍙叉祦姘村彿 | 瑙勫垯涓昏〃瀛樺綋鍓嶅€硷紝鏃ュ織琛ㄥ瓨瀹為檯钀藉彿鍊?|

| 鎶勯€侀厤缃?| `menuId` | 鎶勯€侀厤缃富妗?| 鑿滃崟褰掑睘閿?| 瀹℃壒鍩熸寜鑿滃崟鍙栨妱閫佷笌杞姙瑙勫垯 |
| 鎶勯€侀厤缃?| `ccRecipientIds` | 鎶勯€侀厤缃富妗?| 鎶勯€佷汉闆嗗悎 | 褰撳墠浠ラ€楀彿鎷兼帴瀛楃涓插瓨鍌?|
| 鎶勯€侀厤缃?| `transferIds` | 鎶勯€侀厤缃富妗?| 杞姙浜洪泦鍚?| 褰撳墠浠ラ€楀彿鎷兼帴瀛楃涓插瓨鍌?|
| 鎶勯€侀厤缃?| `status` | 鎶勯€侀厤缃富妗?| 閰嶇疆鍚仠鐘舵€?| 涓氬姟璇诲彇鏃跺彧鐪嬪惎鐢ㄩ厤缃?|

| SKU渚涘簲鍟嗗簱 | `supplierId` | 渚涘簲鍟嗗綊灞炵储寮?| 渚涘簲鍟嗗綊灞為敭 | 瀹氫綅鏌?SKU 褰掑睘鍝釜渚涘簲鍟?|
| SKU渚涘簲鍟嗗簱 | `skuId` | 渚涘簲鍟嗗綊灞炵储寮?| SKU 褰掑睘閿?| 杞婚噺鍏崇郴涓婚敭涔嬩竴 |
| SKU渚涘簲鍟嗗簱 | `productionProcess` | 渚涘簲鍟嗗綊灞炵储寮?| 宸ヨ壓鎽樿鍐椾綑 | 浠庢寮?SKU 澶嶅埗鏉ョ殑鎽樿瀛楁 |
| SKU渚涘簲鍟嗗簱 | `factorySkuNo` | 渚涘簲鍟嗗綊灞炵储寮?| 宸ュ巶娆惧彿鎽樿鍐椾綑 | 浠庢寮?SKU 澶嶅埗鏉ョ殑鎽樿瀛楁 |
| SKU渚涘簲鍟嗗簱 | `surfaceTechnics` | 渚涘簲鍟嗗綊灞炵储寮?| 琛ㄩ潰宸ヨ壓鎽樿鍐椾綑 | 浠庢寮?SKU 澶嶅埗鏉ョ殑鎽樿瀛楁 |

## 瀹㈡埛渚?SKU 鍏崇郴鍩熻ˉ鍏呰涔?- `wh_customer_sku.customerSkuNo`锛氱幇琛屽鎴锋鍙蜂富涓氬姟閿箣涓€锛屾寜 `customerId + customerSkuNo + 鍚敤鎬乣 鍒ら噸銆?- `wh_customer_sku.saleGramCmtFee`锛氬鎴蜂晶閿€鍞厠宸ヨ垂锛屼笉鏄嫭绔嬪綍鍏ュ瓧娈碉紝鑰屾槸 `basicGramCmtFee + moreGramCmtFee` 鐨勮仛鍚堢粨鏋溿€?- `wh_customer_sku.isDirect`锛氬鎴锋鐩撮噰鏍囪锛屽彲鎸夊鎴锋鍙锋壒閲忓弽鏌ョ洿閲囨銆?- `wh_customer_sku.status`锛氫粎鏈夊惎鍋滆涔夛紱鎺у埗鍣ㄥ垹闄ゆ帴鍙ｅ疄闄呰蛋鍋滅敤锛屼笉鍋氱墿鐞嗗垹銆?- `wh_sku_customer.customerSkuNo/customerSkuName`锛氭棫绱㈠紩灞傞噷鐨勫鎴锋鍙?娆惧悕锛屽垱寤烘椂鐩存帴澶嶅埗姝ｅ紡 SKU 缂栫爜鍜屽悕绉般€?- `wh_sku_customer_lm.outSkuNo`锛氳€佸簷澶栭儴娆惧彿锛涘綋鍓嶅垱寤烘椂鐩存帴澶嶇敤姝ｅ紡 `skuNo`銆?- `wh_sku_customer_lm.skuUuid`锛氳€佸簷鍞竴缂栧彿锛涘綋鍓嶅悓鏍风洿鎺ュ鐢ㄦ寮?`skuNo`锛屽苟鏈湡姝ｇ嫭绔嬬紪鐮併€?- `wh_sku_customer_lm.productType/topClass/processClass/skuClass/fiveClass`锛氳€佸簷娓犻亾鍟嗗搧鍒嗙被瀛楁锛屾潵婧愪簬姝ｅ紡 SKU 鏂囨湰鍒?`Lm*Enum` 鐨勬槧灏勭粨鏋溿€?

## 娆惧紡妯″彿琛ュ厖璇箟
- `modelNo`锛氭ā鍙蜂富涓氬姟閿紝褰撳墠鎸夊叏琛ㄥ敮涓€鏍￠獙銆?- `segmentNo`锛氶噸閲忔缂栧彿锛涗负绌烘椂鎵嬪伐寤烘。榛樿鍐?`00`銆?- `bigType`锛氭ā鍙锋墍灞炲ぇ鍒嗙被鍙ｅ緞锛岀悊璁轰笂搴斾笌閲嶉噺娈靛拰 SKU 鐨勫ぇ绫讳繚鎸佷竴鑷淬€?- `minGramWeight/maxGramWeight`锛氭ā鍙峰懡涓殑閲嶉噺娈佃竟鐣屻€?- `sampleGramWeight`锛氫粠姝ｅ紡 SKU 鎶曞奖杩囨潵鐨勬牱鐗堝厠閲嶃€?- `shareStatusLm`锛氳€佸簷鍏变韩鐘舵€侊紝`1=鍏变韩`銆乣0=涓嶅叡浜玚銆?

## 鍏变韩搴撳瓨琛ュ厖璇箟
- `shareStock`锛氱疮璁″彲鍏变韩搴撳瓨锛屼笉绛変簬褰撳墠缁撲綑搴撳瓨銆?- `surplusStock`锛氬綋鍓嶅叡浜粨浣欏簱瀛橈紝鏄€佸簷瀵硅处鍜屽樊寮傝皟骞崇殑涓诲彛寰勩€?- `lockStock`锛氬鎴烽攣瀹氬簱瀛樻暟閲忋€?- `diffStock`锛氬緟璋冨钩宸紓閲忥紝涓嶆槸姝ｅ紡搴撳瓨锛岃涔堣璋冨钩娓呴浂锛岃涔堟寔缁寕璐︺€?- `serialNumber`锛氬叡浜祦姘翠笌鑰佸簷鎺ュ彛鐥曡抗鐨勪覆鑱斾富閿€?- `shareStatusLm`锛氳櫧鐒跺瓧娈典笉鍦ㄦ湰琛紝浣嗗叡浜摼宸茬粡涓婃父渚濊禆妯″彿灞傜殑鑰佸簷鍏变韩寮€鍏炽€?

## 鍏变韩鏂硅ˉ鍏呰涔?- `code`锛氬叡浜柟缂栫爜锛屾槸鍏变韩鏂逛富妗ｇ殑璇嗗埆瀛楁涔嬩竴銆?- `customerId`锛氬叡浜柟鍏宠仈瀹㈡埛銆?- `userId`锛氬叡浜柟璐ｄ换浜?鑱旂郴浜鸿处鍙枫€?- `status`锛氬叡浜柟鍚仠鐘舵€併€?- `localId`锛氬叡浜柟缁戝畾搴撲綅閿紱鍙湁 `stockShare = true` 鐨勫簱浣嶆墠鍏佽杩涘叆鍏变韩鏂瑰叧绯昏〃銆?

## 鍏变韩娴佹按涓庤€佸簷鎺ュ彛鐥曡抗琛ュ厖璇箟
- `serialNumber`锛氬叡浜祦姘翠笌鑰佸簷鎺ュ彛鐥曡抗鐨勪覆鑱斾富閿€?- `status`锛氬叡浜祦姘村悓姝ョ姸鎬侊紝涓嶇瓑浜庡惎鍋滅姸鎬併€?- `enable`锛氬叡浜祦姘磋繍缁寸鐢ㄦ爣璁帮紝`flowRecordDiscard` 鏀圭殑鏄畠銆?- `failReason`锛氬叡浜祦姘存渶杩戜竴娆″け璐ュ師鍥犮€?- `resultCode/resultMsg`锛氳€佸簷鎺ュ彛璋冪敤缁撴灉鐮佷笌缁撴灉鎻愮ず銆?- `callback*`锛氳€佸簷鎺ュ彛寮傛鍥炶皟鐥曡抗瀛楁闆嗗悎锛岃鏄庢帴鍙ｈ〃鍏峰鍥炶皟闂幆璁捐銆?

## 鑰佸簷璁㈠崟涓庡洖璋冩帴鍙ｈˉ鍏呰涔?- `LmApiResult.status`锛氳€佸簷鍗忚杩斿洖鐘舵€佺爜锛屼笉绛変簬绯荤粺鍐呴儴閫氱敤鎴愬姛鐮併€?- `order_no/order_state/order_msg`锛氳€佸簷璁㈠崟鍗忚鍗曞ご杩斿洖瀛楁銆?- `order_list`锛氳€佸簷鍗忚琛岀骇缁撴灉闆嗗悎銆?- `task_id`锛氳€佸簷鍥炶皟浠诲姟鍙凤紝鏄洖璋冨崗璁噷鐨勪笂娓镐换鍔℃爣璇嗐€?- `wh:callback:lm`锛氳€佸簷鍗忚鍏ュ彛缁熶竴鏉冮檺閿紝褰撳墠瑕嗙洊涓嬪崟涓庡洖璋冧袱缁勬帴鍙ｃ€?

## 鎺ュ彛鍚屾浠诲姟琛ュ厖璇箟
- `taskNo`锛氬悓姝ヤ换鍔″彿锛屽綋鍓嶈嚜鍔ㄧ敓鎴愬彛寰勬槸鏃堕棿鎴冲瓧绗︿覆銆?- `bizType`锛氬悓姝ヤ换鍔＄殑绗竴涓氬姟瑙ｉ噴閿紝姣?`apiKey` 鏇撮噸瑕併€?- `outTaskId`锛氬閮ㄤ换鍔″彿銆?- `callbackStatus`锛氬洖璋冩墽琛岀粨鏋滅姸鎬併€?- `callbackResult`锛氬洖璋冨鐞嗙粨鏋滐紝`0=寰呭鐞哷銆乣1=宸插鐞哷 杩欑被涓氬姟璇箟瀛楁銆?- `callbackReply`锛氬洖璋冨鐞嗗悗鐨勫簲绛斿唴瀹圭暀鐥曘€?

## 2026-03-31 鍏变韩搴撳瓨鍙ｅ緞琛ュ厖
- 鏃х増鍏变韩搴撳瓨锛歚wh_stock_share.num`=鏈湴鍏变韩搴撳瓨锛宍numRemote`=杩滅搴撳瓨锛宍numSync`=寰呭悓姝ラ噺锛宍numLock`=璁㈠崟閿佸畾閲忋€?- 鏂扮増鍏变韩搴撳瓨锛歚wh_share_stock.shareStock/surplusStock/lockStock/diffStock` 鏄彟涓€濂楀彛寰勶紝涓嶅彲鐩存帴娣风敤銆?- `wh_item_share.syncStatus`锛氬崟浠跺叡浜叧绯讳笂鐨勬渶杩戜竴娆″悓姝ョ粨鏋滃揩鐓с€?- `wh_stock_share_log.numBefore/numChange/numAfter`锛氭棫鐗堝叡浜簱瀛樹簨浠堕摼鐨勬牳蹇冭拷婧瓧娈点€?

## 2026-03-31 鍑鸿揣灞ョ害鍙ｅ緞琛ュ厖
- `wh_delivery_bill.status`锛氬緟鍑鸿揣/鍑鸿揣涓?宸插嚭璐?鍑鸿揣澶辫触/浣滃簾杩欑粍灞ョ害鐘舵€侊紝涓嶇瓑浜庨攢鍞粨绠楃姸鎬併€?- `wh_delivery_bill.serialNumber`锛氳€佸簷鍑鸿揣鎺ㄩ€佹帴鍙ｆ壒娆″彿锛屽鎺?`wh_lm_api_transfer.serialNumber`銆?- `customerorder.deliveryNotify`锛氭渶杩戜竴娆¤€佸簷鍑鸿揣鍥炶皟鎽樿锛屼笉鏄畬鏁存帴鍙ｆ姤鏂囥€?- `salebill.syncStatus=ONGOING`锛氬湪鍑鸿揣鍥炶皟鎴愬姛鍦烘櫙涓嬭〃绀哄悗缁粛鏈夊叡浜簱瀛樺紓姝ラ摼鏈畬缁撱€?

## 2026-03-31 瀹㈡埛璁㈠崟鍙ｅ緞琛ュ厖
- `orderStatus`锛氬唴閮ㄥ饱绾?浣滀笟鐘舵€併€?- `outOrderStatus`锛氬閮ㄥ钩鍙扮姸鎬侊紝涓嶇瓑浜庡唴閮ㄧ姸鎬併€?- `syncStatus`锛氬澶栧悓姝ョ姸鎬侊紝涓嶇瓑浜庡嚭璐ф垚鍔熺姸鎬併€?- `deliveryNotify`锛氭渶杩戜竴娆¤€佸簷鍑鸿揣鍥炶皟鎽樿锛屼笉鏄畬鏁存帴鍙ｆ姤鏂囥€?- `outOrderNo / outPpOrderNo / outTaskId`锛氬垎鍒搴斿閮ㄨ鍗曞彿銆丳P 鍗曞彿鍜屽閮ㄤ换鍔″彿銆?

## 2026-03-31 瀹㈡埛璁㈠崟鎺ュ崟鍙ｅ緞琛ュ厖
- `UN_SUBMIT / WAIT_TAKING / WAIT_PICKING / WAIT_COMEBACK`锛氫富瑕佸睘浜庡鎴疯鍗曟帴鍗曞眰澶勭悊鑼冨洿銆?- `customerorderreceive.submit(...)`锛氭妸鏈彁浜よ鍗曟帹杩涘埌鍙嫞璐у叆鍙ｏ紝涓嶇瓑浜庡饱绾︽帴鍗曘€?- `customerorderreceive.taking(...)`锛氱湡姝ｇ殑涓氬姟鎺ュ崟鍔ㄤ綔锛岃Е鍙戣鍗曟秷鎭埛鏂般€?

## 2026-03-31 瀹㈡埛璁㈠崟鐪嬫澘鍙ｅ緞琛ュ厖
- `getKanbanOrder`锛氭贩鍚堝睍绀烘棩鎶ユ柊澧為噺涓庡叏閲忕疮璁￠噺銆?- `getKanbanSku`锛氭寜 `skuNo + modelNo + specs` 鑱氬悎锛屼笉鍖哄垎鏇寸粏涓氬姟缁村害銆?- 鐪嬫澘閲岀殑鍙戣揣閲忔潵婧愪簬 `customer_order_item`锛屼笉鏄洿鎺ュ彇璁㈠崟鐘舵€併€?

## 2026-03-31 瀹㈡埛鍏变韩搴撲綅鍙ｅ緞琛ュ厖
- `wh_customer_location`锛氬喅瀹氬鎴峰弬涓庡叡浜簱瀛樼殑搴撲綅鑼冨洿銆?- `customer-location /page`锛氭湰璐ㄦ槸鈥滀粨搴撳叏搴撲綅 + 瀹㈡埛鍏变韩鐘舵€佲€濈殑瑙嗗浘锛屼笉鏄崟琛ㄥ垎椤点€?
## 2026-03-31 瀹㈡埛瀛楀嵃鍙ｅ緞琛ュ厖
- markingNo锛氬鎴峰瓧鍗板叏灞€鍞竴缂栧彿锛屼笉绛変簬瀹㈡埛鍐呭眬閮ㄧ紪鍙枫€?- content锛氬鎴峰瓧鍗版鏂囷紱鍦ㄥ鎴风淮搴﹀唴鍘婚噸锛屼篃鏄鍗?printContent 鐨勭湡瀹炴潵婧愩€?- cquiesce锛氶粯璁ゅ瓧鍗版爣璁帮紱搴旂敤灞備細鎶婂悓瀹㈡埛鍏朵粬榛樿鍊兼竻闆躲€?- markingType锛氬瓧鍗扮被鍨嬪瓧鍏稿彛寰勶紝椤甸潰灞曠ず鍚嶇О渚濊禆瀛楀吀缈昏瘧銆?- pics锛氬瓧鍗板浘鐗?URL锛屼笉鍙備笌榛樿閫昏緫锛屼絾灞炰簬瀛楀嵃涓绘。鐨勪竴閮ㄥ垎銆?
## 2026-03-31 瀹㈤攣绠＄悊鍙ｅ緞琛ュ厖
- customerLock锛氬崟浠朵笂鐨勫鎴烽攣瀹氭爣璁?褰掑睘鍙ｅ緞锛屽閿佺鐞嗛〉鐪熸鎸夊畠杩囨护銆?- ilterCustomerLock锛氫笉鏄暟鎹簱瀛楁锛岃€屾槸鍗曚欢鍒嗛〉鏌ヨ閲岀殑鈥滃彧鐪嬪閿佲€濊繃婊ゅ紑鍏炽€?- izType = CUSTOMER_LOCK锛氬崟浠舵棩蹇楅噷鐨勫閿侀噴鏀句簨浠剁被鍨嬶紝涓嶆槸鐙珛鍗曟嵁绫诲瀷銆?




## 2026-03-31 字印单口径补充
- illStatus：不只是审核态，还包含 MARKING / MARKING_FINISHED / BACKING 这类执行态。
- printStatus：字印明细执行态，marked(...) 会整单改成已字印。
- originType/originBillId/originBillNo：字印单来源业务单识别口径。
- 	ransferBillId/transferBillNo：字印承运调拨挂接口径。
- markingNo：客户字印编号，不是字印单自己的流水号。

## 2026-03-31 小包口径补充
- 	rayNo：产品包内小包编号，通常是 packNo-trayOrder。
- 	rayOrder：包内盘次号，不是全局排序号。
- 	otalGrossWeightRepeat：复核毛重口径，产品包审核会依赖它做校验。
- 	otalGrossWeightPackage：展示用总毛重，等于复核毛重再加不干胶重量配置。

## 2026-03-31 小包明细与字印单明细口径补充
- grossWeightRepeat：小包明细复核毛重基础字段，产品包审核会继续依赖它。
- invDevice：小包明细采集设备来源口径，不只是展示字段。
- printStatus：字印明细执行态，不等于字印单单头 illStatus。
- effective：字印明细有效标记，作废时会整单改失效。

## 2026-03-31 产品包明细与成品转料明细口径补充
- 	rayNo：产品包明细的小包归组口径。
- marking：产品包明细上的字印需求标记，字印链会直接按它筛待字印数据。
- grossWeightRepeat：产品包明细/小包复核毛重关键字段之一。
- listDataSummary：成品转料明细报表里的尾行汇总，不是普通明细行。

## 2026-03-31 收款分账明细与订单核销明细口径补充
- illNo：收款分账明细的分组查询主键，也是整组分账结果的归集键。
- cutoffWriteoffSum：订单核销明细报表里的截止累计核销量。
- 	otalWriteoffNum：报表层实时计算值，等于 cutoffWriteoffSum + writeoffNum。
- goodsSkuNo：订单核销明细转成品调拨引用时的成品 SKU 识别口径。

## 2026-03-31 配件域口径补充
- `accessoriesarchives`：辅料最外层档案池，不等于最终商品档案。
- `code`：辅料类别编码，是辅料类别导入与回查主键。
- `categoryNo`：辅料分类编号，是辅料商品和库存链的分类识别键。
- `goodsNo`：辅料商品编号，也是辅料库存与库存日志的商品识别键。
- `status`：辅料入库/出库单头状态，当前清晰可见的是 `INIT/CONFIRM/INFIRM/DISCARD` 这组单据状态流。
- `payed`：辅料入库/出库单的付款标记，和库存增减不是同一维度。
- `semisTransferNo`：辅料入库明细被半成品调拨引用后的挂接单号。
- `bizNo`：辅料库存日志的业务单号预留口径。

## 2026-03-31 资金主数据口径补充
- `accountCode`：存欠账户编码，当前自动编号口径为 `A + 6位流水`。
- `accountType`：账户类型，是客户/供应商财务口径分流键。
- `walletTypeNo`：钱包类型编号，决定现金钱包或材质钱包语义。
- `materialNo`：钱包材质识别键；现金钱包固定为 `-`。
- `settleClass`：钱包结算类别，是钱包唯一键的一部分。
- `bizNo`：钱包日志/银行卡日志的重要业务单号追踪键。
- `bankCode`：网点银行卡唯一识别码，也是银行卡日志回填账户名的关键键。

## 2026-03-31 组织与库存主数据口径补充
- `shopIds`：用户直接绑定的可见网点范围。
- `deptId`：网点、业务员和分区过滤链的重要组织键。
- `areaNo`：业务分区编号，当前是 3 位自增口径。
- `userId = staffId`：业务员与系统用户强绑定的关键设计。
- `walletTypeNo`：原料对应的钱包类型映射键。
- `timestamp`：成品库存、原料库存更新时的乐观锁时间戳。
- `bizNo`：成品/原料库存日志的业务单号追踪键。

## 2026-04-01 报表与任务口径补充
- `scanStatus`：盘点任务明细扫描状态，至少覆盖未扫、已扫、盘盈三类语义。
- `taskStatus`：打印任务单状态，当前清晰可见的是 `NONE_PRINT / PRINTING / PRINTED / DISCARD`。
- `printCount`：本轮任务的打印次数，可被重置清零。
- `totalPrintCount`：累计打印次数，不随任务重置一起清零。
- `entryBillId`：打印任务与单件入库单的挂接键。
- `group`：即时库存报表的分组键，当前支持仓库和网点维度。

## 2026-04-01 结算与调拨明细层口径补充
- `settleAmount`：结价单里按 `settleWeight * settlePrice` 重算的结算金额，不完全信任前端提交值。
- `stockWeightNow/stockMoneyNow`：结价建单时按当前账户钱包现状冻结的快照字段。
- `notArrivedGramWeight`：需求单明细里按均值克重和未到货件数重算的“未到货重量”口径。
- `finishReason/factoryAnswer/outsourcingOrderNo`：需求单明细分页里从委外明细 `GROUP_CONCAT` 聚合回来的展示字段。
- `settleNo/settleType`：销售/退货结算明细真实结算方式口径，展示阶段再由 `settlement` 主数据回补。
- `walletTypeNo`：销售/退货结算明细展示时由材质主数据反查的钱包类型映射键。
- `summary`：单件调拨工序报表附带的汇总对象，不属于普通明细行。
- `billId / billNo`：成品调拨明细最常用的两组归集键，分别承接主键关联和业务单号查询。

## 2026-04-01 财务明细卫星层口径补充
- `bankCode`：收款/付款明细按银行卡维度回查的识别键，不等于单头主键。
- `summary`：收款/付款明细导出时手工追加的汇总记录，不是普通明细行。
- `customerNameAux`：客户应收调整明细分页里的客户辅助名反查入口，不是最终落表字段。
- `bizType`：供应商应付调整明细在分页页会转成业务文案，导出当前未必同口径。
- `billId`：调账单、收付款单、单件调整单 detail 层最常用的主单归集键。
- `accessoriesList`：单件调整明细详情页挂接的配件卫星集合，不在主单层。

## 执行明细与日志卫星层字段语义补充
- `entrybilldetail.itemId/epc/itemNo`
  - 单件入库审核后反写的新单件身份字段，不是建单时就有的静态输入。
- `materialinbounddetail.convertWeight`
  - 来料钱包口径重量。
- `materialinbounddetail.originWeight`
  - 来料库存口径重量。
- `materialoutbounddetail.convertWeight`
  - 出料钱包口径重量。
- `materialoutbounddetail.originWeight`
  - 出料库存口径重量。
- `maintaininbilldetail`
  - “最近一次维修回收结果”当前实际按最大 `id` 推断。
- `maintaininbilldetaillog.bizType/sourceBillId`
  - 不只是日志字段，还会决定是否自动生成客户留货及客留来源单挂接。
- `maintainoutbilldetail.billStatus`
  - 在多条校验链里直接用字符串 `"1"/"3"` 判断已审核/作废。

## 单件日志、盘点卫星与 SKU 库存层字段语义补充
- `itemlog.billStatus`
  - 日志创建时会被固定写成 `"0"`，不一定等于来源单真实审核态。
- `iteminventorybillitem.inventorySource`
  - 运行时会被翻译成盘盈/盘亏，不是单纯来源字段。
- `iteminventorybillstock`
  - 代表盘点建单时冻结的账面库存快照，不是实时库存。
- `skustock.single`
  - SKU 库存统计维度的重要分流字段，决定按单件还是按成品聚合。
- `skustockdaily.statDate`
  - SKU 库存日报统计日；`initDaily()` 会按该日期先删后建。

## 明细事实层与资金日结卫星字段语义补充
- `customerorderdetail.sampleGramWeight/sumGoldWeight`
  - 由当前 SKU 样重运行时补齐并计算，不是明细固化快照。
- `goodsrejectbilldetail.reason`
  - 退厂原因展示依赖原因字典映射。
- `inventoryweighingdetail.profitLossNum/profitLossGoldWeight`
  - 可被 `updateClearProfitLoss(...)` 整单清零，不是稳定历史值。
- `profitlossadjustdetail.materialNo/goldWeight`
  - 原料库存盈亏汇总的直接输入口径。
- `walletdaily.statDate`
  - 钱包日结业务日，差异检查按昨日 daily + 当日 walletlog 推导。
- `shopbankdaily.statDate`
  - 银行卡日结业务日，差异检查按昨日 daily + 当日 bank log 推导。

## 规则、日志与记录尾项字段语义补充
- `inrejectreason.type/code/reason`
  - 已直接参与成品入库/退厂报表原因翻译，不是纯后台字典字段。
- `skurulelog.skuNo`
  - 用于把某次编码日志挂回正式 SKU。
- `skusearchlog.userIds/userNickname`
  - 页面查询时会先把昵称解析成用户 id 集合再查日志。
- `stockmessagerecord.status/failReason`
  - 表达库存消息的发送/处理结果态。
- `shareflowrecord.serialNumber/status/failReason`
  - 表达共享库存动作的串号、同步状态和失败原因。
- `lmapitransfer.serialNumber`
  - 老庙接口链的关键关联键。

## 执行尾项明细字段语义补充
- `cancelbilldetail.billId/itemNo/skuNo`
  - 单件作废事实层的核心挂接字段，分页还会回填单头状态和仓店。
- `distributionbilldetail.packId/saleBillId`
  - 铺货明细到产品包和销售单来源的挂接键。
- `shelvesbilldetail.epc`
  - 库位调整明细回查当前单件的关键键。
- `unitpricemaintaindetail.billNo`
  - 单价维护整批删除与重建的关键键。

## 半成品、转料与客留尾项字段语义补充
- `recycledetail.isScan/erpConfirm`
  - 转料明细导出时会运行时翻译成展示文本。
- `semistransfer.status`
  - 不只是审核态，还覆盖收货、拒签、作废等完整流转状态。
- `materialinventorydetail.walletTypeNo/wareNo`
  - 原料盘点明细当前暴露的主要查找口径。
- `customerretain.status/packNo/saleNo`
  - 留货状态、产品包和销售结算挂接的核心字段。

## 价格、盘点与铺货尾项字段语义补充
- `distributionbill.status`
  - 铺货单执行状态，驱动单件迁移链。
- `recyclebill.billStatus`
  - 单件转料审核状态，直接关联库存和单件状态回写。
- `inventorybill.moreNumber/lessNumber/moreWeight/lessWeight`
  - 旧版盘点单头汇总字段，审核时运行时重算。
- `materialinboundsettle.settleNo/materialNo`
  - 来料结算补材质和结算方式的关键键。
- `unitpricemaintain.priceInfo`
  - 运行时拼出来的展示字段，不是持久化原始字段。
- `unitprice.walletTypeNo,settleClass`
  - 正式单价主档的唯一口径键。
- `walletcredit.accountId/status`
  - 授信额度主档的核心查找口径。

## 商品与 SKU 尾项字段语义补充
- `categoryfive.fiveClassifyNo`
  - 五级分类主数据的重要外部映射键。
- `categoryspec.specNo`
  - 品类规格主数据的核心编号。
- `bigweightsegment.segmentNo/bigType`
  - 大类重量段规则的核心组合键。
- `skufavorite.tagId/userId/skuId`
  - 收藏关系的核心键，`favorite(...)` 实际按这个组合做 toggle。
- `skuseriesmapping.seriesId/skuId`
  - 系列映射的核心组合键。
- `skusetmapping.setNo/setName`
  - 套式映射主键及其回写到 SKU 的核心字段。

## 客户与组织尾项字段语义补充
- `customercontacts.openId/expireTime/miniLoginCode`
  - 联系人登录态、临时联系人有效期和小程序码 URL 的核心组合。
- `customerstaffupdate.bizAdjustDate/processInstanceId/billStatus`
  - 客户归属迁移生效时间、流程实例和审批态的主控制字段。
- `customerstaffupdatedetail.areaId/afterAreaId/businessStaffId/afterBusinessStaffId`
  - 归属迁移前后差异的核心字段组。
- `businessareadept.areaId/deptId`
  - 分区与部门映射的核心组合键。
- `skushop.shopId/skuId/moreGramCmtFee/moreItemCmtFee`
  - 网点款式索引和网点侧费用字段。
- `exhibition.customerId/businessAreaId/businessStaffId/businessManageId`
  - 展销会里固化客户归属快照的关键字段。
- `qcstaff.staffNo/status`
  - 质检员工号主数据的核心查找键。

## 客户订单单件与原料品质尾项字段语义补充
- `customerorderitem.orderId/orderNo/epc/saleBillId`
  - 客户订单履约单件事实的核心组合字段。
- `customerorderitem.marking`
  - 字印回写口径字段，按订单号 + EPC 批量更新。
- `itemsale.itemId/epc`
  - 单件销售落点的核心键。
- `materialquality.qualityNo/bigClassNo/rememberNo/walletTypeNo`
  - 原料品质规则被多条业务链复用的关键编码字段。

## 资金与消息尾项字段语义补充
- `goldpricesetting.code/price/unit`
  - 金价配置主档的核心字段，当前重点使用 `INVEST` 这一路。
- `goldpricerule.startTime/endTime/type`
  - 金价时段规则的核心命中字段。
- `internalremittance.totalRemitAmount/billStatus/remitDate`
  - 内部转账单头的核心控制字段。
- `internalremittancedetail.payerBankId/payeeBankId/remitAmount`
  - 内部转账逐行落账的核心字段。
- `ordermessage.userId/businessStaffId/degreeImportance/readStatus`
  - 订单消息待办归属、重要级别和阅读状态的核心字段。

## 盘点批次与历史库存尾项字段语义补充
- `inventoryweighingbillstock.billId/wareId`
  - 盘点称重库存备份快照的核心定位字段。
- `iteminventorybillbatch.billId/batchNo`
  - 单件盘点批次的核心组合字段。
- `itemhistorystock.stockDate/stockDay/itemStatus`
  - 单件每日库存快照的核心日期与状态字段。
- `historysegmentstock.segmentName/minWeight/maxWeight`
  - 重量段汇总快照的核心分组字段。

## 辅石与配件卫星尾项字段语义补充
- `entrybilldetailaccessories.billId/detailId`
  - 单件入库明细辅石快照的来源定位字段。
- `entrybilldetailaccessories.semisSkuNo/semisPartNo/semisSkuName`
  - 辅石快照上的半成品来源痕迹字段。
- `itemaccessories.itemNo`
  - 正式单件辅石事实的关联键，当前实现里承担单件号/EPC 级别语义。
- `supplierskuaccessories.skuNo`
  - 供应商 BOM 辅石卫星的核心清理与查询键。
- `supplierskupart.skuNo`
  - 供应商 BOM 配件卫星的核心清理与查询键。

## 分析报表尾项字段语义补充
- `supplierqualityreport.supplierId/skuId/categoryId/reportDate`
  - 供应商品质分析结果的核心分组字段。
- `departmentlossreport.deptId/skuId/reportDate`
  - 部门折损分析结果的核心分组字段。
- `departmentlossreport.totalLoss*`
  - 部门折损分析里被 `getLossTotal(...)` 汇总消费的关键损耗字段。

## 预处理与占料分析尾项字段语义补充
- `saleprepossessing.customerOrderNo/markingNo/status`
  - 结算预处理宿主的核心关联与状态字段。
- `saleprepossessing.shipmentComeGo/logisticsInsurance`
  - 以 JSON 字符串存储的预处理配置字段。
- `reportcustomerareaowe.bizDate/area/customerNo`
  - 客户/分区日均占料分析的核心查询维度。
- `reportcustomerareaowe.au999Today/au999Avg/moneyToday/moneyAvg/mentalToday/mentalAvg`
  - 占料分析页的六个核心结果字段。

## 柜台与周转率报表尾项字段语义补充
- `reporttransferwarehouse.bizDate/shopName/wareName`
  - 周转率报表页的核心查询与展示维度。
- `salereportware.bizDate/group/wareId`
  - 柜台销售统计页的核心入口参数。
- `salereportware.*Single/*Divide`
  - 单件口径与分摊口径的专用统计入口标识。
