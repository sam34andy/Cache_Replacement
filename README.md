# Cache_Replacement
2019 Data Structure Course Homework Three

### 題目: Cache_Replacement

### 使用語言: C++

### 要求:
    1.讀取一個檔案
    2.可以自行設定Block size
    3.可以自行設定Cache total size    
    4.可以自行設定Set associative number
    5.提供至少兩種cache replacement strategy，並可以選擇要使用哪一種strategy

### 編譯方式:
	1.使用Dev-C++、Visual Studio等程式完成編譯
	2.linux "g++ hw3.cpp HW3.txt -o hw3"
	
### 範例input(using txt file "HW3.txt"):
    C64E5
    C64C5
    C74EA
    C64E8
    C64CF
    F64E6
    F74EA
    F84E6
    C74CF
    C64E6
    C64C9
    C74E8

### 範例output:
    1.根據程式提示輸入Block size、Cache total size、set associative number與選定的cache replacement strategy
    2.程式會依序將原輸入檔案的Address、Address(binary)、Tag(binary)、Index(binary)、Offset(binary)、Index(Decimal)輸出
    3.每讀取一條Address就會新增一條分隔線，只要cache裡的Index曾經被寫入過資料就會顯示在分隔線中
![image](https://github.com/sam34andy/Cache_Replacement/blob/master/hw3_output.JPG)

### 程式碼內容:
    #include <iostream>
    #include <fstream>
    #include <string>
    #include <vector>
    #include <cmath>
    #include <sstream>
    #include <bitset>
    using namespace std;

    struct Addr {
      string Text_Original;
      string Text;
      string Tag;
      string Index;
      string Offset;
      int index_dec; // set_index
      int counter; // ?
    };

    struct History {
      string Word;
      bool Hit;
    };

    struct Line {
      string line_word;
      string line_tag;
      int line_counter;
    };

    Line *cache;
    int *set_history;
    vector<Addr> addr;
    vector<History> history;
    void Data_Transfer(int, int, int);
    void Cache_Replacement(int, int, int);
    int Binary_To_Decimal(string);
    void print(int, int, int);
   
### 資料儲存形態與內容:
	cache:所有的line。
	set_history:紀錄每個set裡面已經放了多少筆資料。例:4 way associative最多能在對應的[i]set中放入四筆資料。
	addr:每個address的資料，包含Tag、Index與Offset的binary數值。也包含Index的decimal數值。
	
### 程式碼呼叫順序與函式執行內容:
	main	// input cache與block的大小並決定要使用的cache replacement strategy
		-> Data_Transfer // 把16進制的address換成2進制，並把Offset, Index, Tag分別進行存取
			-> Binary_To_Decimal // 把Index換成十進制的數字，方便其他地方使用
		-> Cache_Replacement
		
	Cache_Replacement執行內容(按順序):
		每個cache的line都有一個counter，用來記錄這筆資料已經多久沒有被使用過。
		1.找到相對應Index的set。給該set裡的每行line的counter+1。並確認是否與最新的資料是同一筆Tag。
		2.(a)if 是相同Tag。在strategy為LRU下，將該counter更新為0。如果strategy為FIFO，則不做事。
		2.(b)if 是不相同Tag，則將該set中counter數字最大的line進行取代。
