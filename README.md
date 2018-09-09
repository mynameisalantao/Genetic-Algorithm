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

今天假設每隻斑馬母體如下
<pre><code>struct  maternal_body{
	int number;  //第幾號母體 
	int gene[gene_length];  //存放基因
	double value;  //適應解
	double fitness;  //適應值(適應值越大表示這是越好的基因) 
};</pre></code>
並且草原上有body_quantity個數量的斑馬
<pre><code>maternal_body body[body_quantity];  //產生body_quantity個母體 </pre></code>
並且定義擁有最佳適應值的斑馬
<pre><code>maternal_body best_body;  //儲存擁有最佳適應值的母體  </pre></code>

先說明演算法的步驟:
1.編碼:依照需要的位數來決定基因的長度(gene_length)，這在程式開始前就要決定好<br/>
2.初始化母體:本程式中void initial(void);部份<br/>
3.計算適應值:本程式寫在void print(void);中，當然在初始化時也有計算<br/>
4.輪盤式挑選基因:本程式寫在void extract(void);這裡我是用輪盤式來寫，擁有較大適應值的母體，會有更高的機率被選上(適者生存)<br/>
5.交配:本程式寫在void mating(void);隨機選取一段區間，將2基因交換<br/>
6.突變:本程式寫在void mutation(void);單一個基因中2元素交換<br/>

主程式
------------
<pre><code>int main(){
	using std::cout;
	srand(time(NULL));  //srand()函數設定初始的亂數種子
	cout<< "初始化";
	initial();  //初始化母體 
	print();  //印出初始化後每個母體中的內容
	//迭代 
	for(int z=1;z < = Iteration;z++){
		cout<<"進行第" << z << "次篩選\n"; 
		extract();  //使用輪盤式，將母體丟入交配池中
		cout<<"進行第" << z << "次交配\n";
	    mating();  //進行交配 
	    cout<<"進行第" << z << "次突變\n";
	    mutation();  //進行突變 
	    print();  //印出目前每個母體中的內容
	    cout<<"\n\n\n";
	}
	system("PAUSE");
	return 0;
} </pre></code>

就是不斷進行這些步驟來迭代<br/>
迭代內容:篩選-交配-突變

初始化母體 
-----------
<pre><code>void initial(){
	int count;  //將母體基因中的數值相加 
	for(int i=0;i<body_quantity;i++){
		count=0;
		for(int j=0;j<gene_length;j++){
			body[i].number=i; 
			body[i].gene[j]=zero_one();  //將每個母體的每個基因指定為0或1的數值 
			count=count+body[i].gene[j]*shift(j);  //將此基因序列從2進位轉成10進位 
		}
		body[i].value=count;  //將母體基因中的數值相加 
		body[i].fitness=fit(body[i].value);  //並將適應解代入適應函數得到適應值 
		if(i==0) best_body=body[i];  //找出擁有最佳適應值的母體，複製到最佳母體 
		else if (body[i].fitness>best_body.fitness) best_body=body[i];
	}
}</pre></code>
先把每個基因的每個元素隨機塞入0或1的數值<br/>
使用自己定義的函數:
<pre><code>#define zero_one()(rand()%2)  //可以隨機產生0或1的數 </pre></code>
透過rand()產生隨機亂數，然後除以2得餘數必定會是0~1 <br/>
然後基因序列從2進位轉成10進位利用到自己定義的函數:
<pre><code>inline int shift(int j){return(0x01 << j);}  //可回傳2的j次方 </pre></code>
把0x01左移j的單位，就可以回傳2進位的次方<br/>
接著將算出來的適應解body[i].value帶入適應函數fit(int)可以得到適應值指定給body[i].fitness <br/>
如果是第一個母體，直接將此母體指定為最佳母體best_body <br/>
接著不斷尋找比這個best_body擁有更好適應值的母體來取代掉它 <br/>

計算適應值並印出
--------------
<pre><code>void print(){
	using std::cout;
	int count;  //將此基因序列從2進位轉成10進位 
	cout << "目前的的母體內容\n";
	for(int i=0;i < body_quantity;i++){
		cout << "#" << body[i].number << "  ";
		count=0;
		for(int j=0;j<gene_length;j++){
			cout << body[i].gene[j] << " ";
			count=count+body[i].gene[j]*shift(j);  //將此基因序列從2進位轉成10進位 
		}
		body[i].value=count;  //將母體基因中的數值相加 
		cout << body[i].value << " ";
		body[i].fitness=fit(body[i].value);  //並將適應解帶入適應函數得到適應值 
		cout << body[i].fitness << " ";
		cout << "\n";
		//找出擁有最佳適應值的母體，複製到最佳母體 
		if (body[i].fitness > best_body.fitness) {
			cout << "要更換了喔\n";
		    best_body=body[i];
		}
	}
	cout << "The best body: ";
	for(int k=0;k < gene_length;k++){
			cout << best_body.gene[k] << " ";
	}
	cout << " " << best_body.value << " "<< best_body.fitness << "\n";
} </pre></code>
就如同初始化一般，不過這次只找擁有比best_body.fitness還要大的body[i].fitness來當作新的best_body

輪盤式
---------
<pre><code>void extract(){
	using std::cout;
	int choose;  //暫存被選中準備要被丟入交配池的母體編號(number) 
	double total_fitness=0;  //全部母體的適應值加總 
	double accumulate_probability[body_quantity];  //每個母體的所占比例累加
	//如果所占比例越大，那麼累加後他所占的區間也會越大，則被選到的機率更大 
	double random;  //產生隨機0~1的數值 
	//全部母體的適應值加總 
	for(int i=0;i < body_quantity;i++)  
	total_fitness+=body[i].fitness;   
	//每個母體的所占比例累加
	accumulate_probability[0]=(double)body[0].fitness/(double)total_fitness;
	for(int i=1;i < body_quantity;i++)  
	accumulate_probability[i]=accumulate_probability[i-1]+(double)body[i].fitness/(double)total_fitness;
	for(int k=0;k < body_quantity;k++){
		random=Rand();  //產生隨機0~1的數值
	    for(int j=0;j < body_quantity;j++){  //尋找到機率區間 
		    if(random < =accumulate_probability[j]){
		    	choose=j;
		    	break;
			}
		    continue;
    	}
	pool[k]=body[choose];
	}
	cout << "準備被丟入交配池的母體編號:\n";
	for(int l=0;l < body_quantity;l++){
		cout << "#" << pool[l].number << " ";
	}
    cout << "\n\n";
    for(int k=0;k < body_quantity;k++){pool[k].number=k;}  //更新為在pool中的編號
    //印出目前在交配池中的母體 
    cout << "丟入交配池\n";
    for(int i=0;i < body_quantity;i++){
		cout << "#"<<pool[i].number << "  ";  //在pool中的編號 
		for(int j=0;j<gene_length;j++){
			cout << pool[i].gene[j] << " ";
		}
		cout << pool[i].value << " ";
		cout << pool[i].fitness << " ";
		cout << "\n";
	}
} </pre></code>

進行交配
---------
<pre><code>void mating(){
	using std::cout;
	int b1,b2;  //從母體編號中，隨機選出2個不相同的編號 
	int g1,g2;  //從基因編號中，隨機選出g2>g1的編號 
	int area;  //要進行交配的基因編號區間 
	int temp;  //暫存要交換的基因元素 
	int count; //將此基因序列從2進位轉成10進位 
	for(int i=0;i < =body_quantity;i++){
		b1=select_one_body();
	    do b2=select_one_body();  //持續隨機產生編號，直到b2的值與b1不相同 
	    while(b2==b1);
	    cout << "b1=" << b1 << " b2=" << b2 << " ";
	    //接下來隨機產生要交換基因的區間
	    //如果基因編號為0~4，那麼g1為0~3，而g2為1~4，並且g2 > g1 
	    g1=select_one_gene();  
	    do g2=select_one_gene()+1;  //持續隨機產生編號，直到b2的值大於b1 
	    while(g2 < =g1);
	    cout << "g1=" << g1<<" g2=" << g2 << "\n";
	    if(Rand() < mating_rate){  //決定是否要交配 
	    	cout << "mating...\n";
	    	for(area=g1;area  <=g2;area++){
		        temp=pool[b1].gene[area];
		        pool[b1].gene[area]=pool[b2].gene[area];
		        pool[b2].gene[area]=temp;
	        }
		}
	    else{
	    	cout << "no mating\n";
	    	continue;
		}
	}
	//印出交配後的基因內容 
    cout<<"交配後\n";
    for(int i=0;i < body_quantity;i++){
    	count=0;
		cout << "#" << pool[i].number << "  ";  //在pool中的編號 
		for(int j=0;j < gene_length;j++){
			cout << pool[i].gene[j] << " ";
		}
		cout << "\n";
	}
    //將交配池pool中完成交換的所有母體複製回body 
	for(int i=0;i < body_quantity;i++){
		body[i]=pool[i];
	}
}</pre></code>


