## 『SQLクックブック 第2版』正誤表

下記の誤りがありました。お詫びして訂正いたします。

| 頁 | 誤 | 正 |
|----|----|----|
| 2章<br />レシピ2.4<br />p.21 | MySQLでは現在はTRANSLATE関数をサポートしていないので、この問題の解決策はありません。| MySQLではsubstringやinstrを組み合わせて使う事で同様の事を実現できます。<br /> enameで並び変える例: <br /> `select * from V order by substring(data,1,instr(data,' ')) asc;`<br />deptnoで並び変える例: <br /> `select * from V order by substring(data,instr(data,' ') + 1,2) asc;`  |

