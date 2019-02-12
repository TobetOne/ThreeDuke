# Tobet Three Duke Verify

验证工具网页请访问：https://tobetone.github.io/ThreeDuke/

在使用此网页工具之前，请仔细阅读以下说明。你可以根据如下说明，自行开发程序验证。
## 开奖结果计算
  1. 根据游戏数据生成随机种子,并签名：
    seed = GameId+BetPlayersCount+LastBetTime;
    seed_sign = sign(seed)
  2. 对随机种子hash（SH256）运算，结果转换为16进制
    hash_hex = hex(sha256(seed_sign))
  3. 牌顺序为 黑桃（S），红桃（H），梅花（C），方块（D）
  'SA', 'HA', 'CA', 'DA', 'S2', 'H2', 'C2', 'D2', 'S3', 'H3', 'C3', 'D3', 'S4', 'H4', 'C4', 'D4',
  'S5', 'H5', 'C5', 'D5', 'S6', 'H6', 'C6', 'D6', 'S7', 'H7', 'C7', 'D7', 'S8', 'H8', 'C8', 'D8',
  'S9', 'H9', 'C9', 'D9', 'S10', 'H10', 'C10', 'D10', 'SJ', 'HJ', 'CJ', 'DJ', 'SQ', 'HQ', 'CQ', 'DQ', 'SK', 'HK', 'CK', 'DK';
  4. 对hash_hex 分别截取5次6位字符转换成数字进行运算,对庄(banker),上门(top),天门(mid),下门(bot)4门进行发牌
  例如：第一轮 取1到6的字符转为数字，得到的数字对牌剩余数量取余得到要发的牌的序号，直到发完4张，
  第二轮 取7到12的字符转为数字，得到的数字对牌剩余数量取余得到要发的牌的序号，直到发完4张，以此类推;

```javascript
one = hexToInt(hash.substring(0, 6));

two = hexToInt(hash.substring(6, 12));

three = hexToInt(hash.substring(12, 18));

player = [banker, top, mid, bot]

bout = [one, two, three];

for (var i = 0; i < bout.length; i++) {

  for (var j = 0; j < player.length; j++) {

    player[j].push(poker[bout[i] % poker.length]);

    poker.splice(bout[i] % poker.length, 1);

        }
   }

bankerResult：player[0]

topResult：player[1]

midResult：player[2]

botResult：player[3]
```
## 随机因子说明
   seed = GameId+BetPlayersCount+LastBetTime
*  GameId:游戏ID
*  BetPlayersCount:参与游戏玩家个数
*  LastBetTime:最后一位投注的玩家的投注时间
## 签名验证
   验证签名使用Tobet的公钥（EOS6CG8VwJ8G1iFn6x781PMojmfD7i4kqqzsgd1AjWwAaEz35QGhn），对seed_sign进行ecc签名验证，验证结果通过即可证明随机种子未被篡改。

