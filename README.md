Genetic-Algorithm 2018/09/09
==============================

基因演算法就是讓基因不斷進行取樣、交配、變異<br/>
某些基因具有生存上的優勢，較適合交配繁衍後代<br/>
比如說有草原上出現了斑馬的天敵如獅子<br/>
跑越快越容易生存，那麼適應解x就是腳力<br/>
但也不是跑越快越好，跑太快超過物理極限腳容易斷掉，照樣被獅子吃掉<br/>
所以要尋找能夠最好在草原上生存的適應值f(x) <br/>
此程式假設今天適應函數 f(x)=-(x-12)^2+174 <br/>
也就是擁有腳力12單位的斑馬最容易生存，其適應值為174 <br/>
如果低於12單位或高於12單位較不易生存<br/>
<pre><code>inline double fit(int value){return(-value*value+24*value+30);}</pre></code>
先說明演算法的步驟:
1.編碼:依照需要的位數來決定基因的長度(gene_length)，這在程式開始前就要決定好<br/>
2.初始化母體:本程式中void initial(void);部份<br/>
3.計算適應值:本程式寫在void print(void);中，當然在初始化時也有計算<br/>
4.輪盤式挑選基因:本程式寫在void extract(void);這裡我是用輪盤式來寫，擁有較大適應值的母體，會有更高的機率被選上(適者生存)<br/>
5.交配:本程式寫在void mating(void);隨機選取一段區間，將2基因交換<br/>
6.突變:本程式寫在void mutation(void);單一個基因中2元素交換<br/>

今天假設每隻斑馬母體如下
<pre><code>struct  maternal_body{
	int number;  //第幾號母體 
	int gene[gene_length];  //存放基因
	double value;  //適應解
	double fitness;  //適應值(適應值越大表示這是越好的基因) 
};</pre></code>
並且草原上有body_quantity個數量的斑馬
<pre><code>maternal_body body[body_quantity];  //產生body_quantity個母體 </pre></code>





