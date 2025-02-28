#include <iostream>
#include <thread>
#include <chrono>
#include <map>
#include <windows.h>

using namespace std;

int cur_r = 2, cur_c = 0;

char map[5][10]=// 定義地圖
	{
		{'B','T','B','B','T','B','B','B','R','T'},
		{'T','R','R','R','R','R','R','R','R','F'},
		{'T','R','R','T','R','B','T','R','R','B'},
		{'R','R','R','R','R','R','R','R','T','B'},
		{'T','B','B','B','B','T','B','B','B','B'},
	};
	
	bool tool[5][10]=// 用來標記哪些格子是可以觸發故事的
 	{
		{false,true,false,false,true,false,false,false,false,true},
		{true,false,false,false,false,false,false,false,false,true},
		{false,false,false,true,false,false,true,false,false,false},
		{false,false,false,false,false,false,false,false,true,false},
		{true,false,false,false,false,true,false,false,false,false},
	};
	
	bool walk[5][10]=// 用來標記哪些格子是可以走的
	{
		{false,true,false,false,true,false,false,false,true,true},
		{true,true,true,true,true,true,true,true,true,true},
		{true,true,true,true,true,false,true,true,true,false},
		{true,true,true,true,true,true,true,true,true,false},
		{true,false,false,false,false,true,false,false,false,false},
	};

const map<int, string> colors = 
{
    {0, "\033[0m"},     // reset
	{1, "\033[33m"},    // yellow
    {2, "\033[36m"},    // blue
	{3, "\033[37m"},    // white
	{4, "\033[31m"},    // red
};

void slowPrint(const string& text,int color)
{
	int charDelay=30;
	int lineDelay=1000;// 每個字 50ms，每行 1000ms
	cout << colors.at(color);
    for (char c : text)
	{
        cout << c << flush;
        this_thread::sleep_for(chrono::milliseconds(charDelay)); // 逐字延遲
    }
    cout << endl;
    this_thread::sleep_for(chrono::milliseconds(lineDelay)); // 換行後的停頓
    cout << colors.at(0);
}

void opening()
{
	slowPrint("「頭好暈……怎麼回事？」",1);  
    slowPrint("你從昏迷中醒來，茫然地抬起頭。四周昏暗，空氣裡瀰漫著陳舊紙張的味道。",3);
    cout<<endl; 
    slowPrint("「這裡是……學校的圖書館？」",1); 
    slowPrint("四下打量後，你又覺得哪裡不太對勁——某些地方，與記憶中的畫面有些偏差。",3); 
    cout<<endl;
    slowPrint("「……不管怎樣，還是先想辦法離開這裡吧。」",1); 
}

void move_cursor(char c) // 移動光標
{
    switch (c) 
    {
    case 'w': // 按下 'w' 或 'W' 向上移動
    case 'W':
        for (int i = cur_r - 1; i >= 0; --i) // 從當前行向上找
        {
            if (walk[i][cur_c]) // 如果找到可編輯的格子
            {
                cur_r = i;
				tool(i,cur_c) // 更新當前行位置
                break;
            }
        }
        break;
    case 's': // 按下 's' 或 'S' 向下移動
    case 'S':
        for (int i = cur_r + 1; i < 5; ++i) // 從當前行向下找
        {
            if (walk[i][cur_c]) 
            {
                cur_r = i; 
				tool(i,cur_c)// 更新當前行位置
                break;
            }
        }
        break;
    case 'a': // 按下 'a' 或 'A' 向左移動
    case 'A': 
        for (int i = cur_c - 1; i >= 0; --i) // 從當前列向左找
        {
            if (walk[cur_r][i]) 
            {
                cur_c = i;
				tool(cur_r,i) // 更新當前列位置
                break;
            }
        }
        break;
    case 'd': // 按下 'd' 或 'D' 向右移動
    case 'D':
        for (int i = cur_c + 1; i < 10; ++i) // 從當前列向右找
        {
            if (walk[cur_r][i]) 
            {
                cur_c = i;
				tool(cur_r,i) // 更新當前列位置
                break;
            }
        }
        break;
    }
}

void library_map(int cur_r ,int cur_c )
{
	for (int i = 0; i < 5; ++i)
  {
        for (int j = 0; j < 10; ++j)
		{
			cout<<map[i][j];
            string style = ""; // 初始化樣式
            if (cur_r == i && cur_c == j)
            { 
                style = "C"; // 光標所在的格子加強顯示
            } 
        }
        cout << endl;
    } 
}

void tool(int a, int b )//觸發道具時輸出故事情節
{
	if(a = 0 || b = 1 )//1
	{
		slowPrint("……這裡是圖書館管理員的辦公室，不過對方似乎不在。",3);
		slowPrint("聽說他很常消失，所以有些人會長期逗留在這裡。",3);
		slowPrint("希望他暫時不要回來吧，不然要解釋會很麻煩。",3);
		
	}
	else if(a  =0 || b = 4 )//2
	{
		slowPrint("一枚古玩被精心地放在展示櫃中。",3);
		slowPrint("看起來很昂貴，還是不要亂碰好了。",3);
	}
	else if(a = 0 || b = 9 )//3
	{
		slowPrint("笑話大全？",3);
		slowPrint("什麼嘛，都是些很冷的諧音梗。",3);
		slowPrint("雖說如此，還是忍不住笑出來了，哈哈哈哈……",3);
	}
	else if(a = 1 || b = 0 )//4
	{
		slowPrint("一朵鳶尾花在花瓶裡靜靜綻放。",3);
		slowPrint("真美麗啊……",3);
		slowPrint("但現在是鳶尾花的花期嗎？",3);
	}
	else if(a = 2 || b = 3 )//5
	{
		slowPrint("一條銀製的繡球花手鍊。",3);
		slowPrint("上面的痕跡不是那麼明顯，似乎常常被拿出來欣賞。",3);
		slowPrint("感覺能從手鍊上黯淡的光芒看出主人的思念。",3);
	}
	else if(a = 2 || b = 6 )//6
	{
		slowPrint("好像有什麼東西竄過去了……！",3);
		slowPrint("你湊近一瞧，是有些老舊的機械發條老鼠。",3);
		slowPrint("伸手轉一下發條，老鼠便一溜煙地跑不見了。",3);
	}
	else if(a = 3 || b = 8 )//7
	{
		slowPrint("地上躺著一條帶有斑斑血跡的手帕。",3);
		slowPrint("你思索幾下，本著好奇心想要伸手撿起。",3);
		slowPrint("不遠處的書櫃突然出現靴子的腳步聲，你嚇得急忙收手。",3);	
	}
	else if(a = 4 || b = 0 )//8
	{
		slowPrint("你伸手拿起一本書，上面寫著「文藝部活動紀錄本」。",3);
		slowPrint("印象中文藝部似乎因為人數問題快解散了？",3);
		slowPrint("你不是很熟悉學校的社團，搖了搖頭將本子放回原位。",3);
	}
	else if(a = 4 || b = 5 )//9 
	{
		slowPrint("有一本書胡亂地丟在整齊的書牆上，封面寫著大大的「都鐸王朝」。",3);
		slowPrint("你對歷史滿有興趣，但現在似乎不是悠哉看史書的時候。",3);
	}
	else if(a = 1 || b = 9 )//終 
	{
		slowPrint("你印象中本該是出口的地方，卻只是一排與周圍毫無二致的巨大書櫃。",3);
		slowPrint("……這樣看來，離開的希望大概是零了吧。",3);
		slowPrint("無奈之下，你隨手翻閱起書架上的書，沒想到竟發現了一本相當奇怪的東西——",3);
		slowPrint("《心理測驗☆快來看看你跟起司的羈絆吧～?》",3);
		slowPrint("……什麼鬼？這東西看起來比 FB 垃圾廣告還蠢。",3);
		slowPrint("但現在也沒別的事可做，於是你帶著半信半疑的心情，開始讀起書中的內容。",3);
	}
	
	else
	{
		break;
	}
}

bool is_moving_action(char c) // 判斷是否為移動光標的動作
{
    return (c == 'W' || c == 'w' || c == 'S' || c == 's' ||
            c == 'A' || c == 'a' || c == 'D' || c == 'd');
}

int psychologicaltest()
{
	char option=0;
	
	cout<<endl; 
	slowPrint("「第一題！對於戀愛小說，你喜歡哪種類型？」",2); 
	cout<<"a.當然是要甜甜的校園戀愛啊！"<<endl;
	cout<<"b.破鏡難圓的劇情非常好吃……"<<endl;
	cout<<"c.談什麼戀愛！自己一個人最棒了！"<<endl;
	while(cin>>option)
	{
		
		if(option=='a')
		{
			slowPrint("「第二題！你覺得人生是由什麼組成的呢？」",2); 
			cout<<"a.未知的意外。"<<endl;
			cout<<"b.過去的因果。"<<endl;
			cout<<"c.相同的事件。"<<endl;
			cout<<"d.無法掌控的命運。"<<endl;
			
			while(cin>>option)
			{
				if(option=='a'){return 1;}
				else if(option=='b'){return 2;}
				else if(option=='c'){return 3;}
				else if(option=='d'){return 4;}
				else{slowPrint("請輸入正確選項！！",4); }
			}
		}
		
		else if(option=='b')
		{
			slowPrint("「第二題！如果被丟到無人島上該怎麼辦呢？」",2); 
			cout<<"a.確保衛生！"<<endl;
			cout<<"b.尋找食物！"<<endl;
			
			while(cin>>option)
			{
				if(option=='a'){return 5;}
				else if(option=='b'){return 6;}
				else{slowPrint("請輸入正確選項！！",4); }
			}
		}
		
		else if(option=='c')
		{
			slowPrint("「第二題！既然是自己一個人的話，那你喜歡什麼樣的生活呢？」",2); 
			cout<<"a.刺激有趣的冒險！"<<endl;
			cout<<"b.開心快樂的玩耍！"<<endl;
			cout<<"c.平靜安穩的日常。"<<endl;
			
			while(cin>>option)
			{
				if(option=='a'){return 7;}
				else if(option=='b'){return 8;}
				else if(option=='c'){return 9;}
				else{slowPrint("請輸入正確選項！！",4); }
			}
		}
		
		else{slowPrint("請輸入正確選項！！",4); }
	}
}

int test_text()
{
	int character=0;
	char user_continue;
	do{
   		character=psychologicaltest();
    	slowPrint("「嗯嗯嗯……我已經知道最適合你的起司啦！你決定好這就是你的起司了嗎？還是要再來一遍？」",2); 
    	cout<<"a.決定好啦！"<<endl;
		cout<<"b.還要再想想……"<<endl;
    	while(cin>>user_continue)
		{
				if(user_continue=='a')
				{
					slowPrint("「好耶！那我們來揭曉答案吧！」",2); 
					break;
				}
				if(user_continue=='b')
				{
					slowPrint("「好，那我們再試一次吧！」",2); 
					break;
				}
				else{slowPrint("請輸入正確選項！！",4); }
		}
	}
	while(user_continue=='b');
	return character;
}

void character_Bech(){
	cout<<endl;
	slowPrint("我中間劇情還沒寫完(尖叫)",2); 
	slowPrint("等全部寫完我再一起放上來好了(陰暗地爬走)",2);  
}

void character_Feichanyin(){
	cout<<endl;
	slowPrint("我中間劇情還沒寫完(尖叫)",2); 
	slowPrint("等全部寫完我再一起放上來好了(陰暗地爬走)",2);  
}

void character_Roselyne(){
	cout<<endl;
	slowPrint("我中間劇情還沒寫完(尖叫)",2); 
	slowPrint("等全部寫完我再一起放上來好了(陰暗地爬走)",2);   
}

void character_Iris(){
	cout<<endl;
	slowPrint("我中間劇情還沒寫完(尖叫)",2); 
	slowPrint("等全部寫完我再一起放上來好了(陰暗地爬走)",2);  
}

void character_Taro(){
	cout<<endl;
	slowPrint("我中間劇情還沒寫完(尖叫)",2); 
	slowPrint("等全部寫完我再一起放上來好了(陰暗地爬走)",2);  
}

void character_Siegfried(){
	cout<<endl;
	slowPrint("我中間劇情還沒寫完(尖叫)",2); 
	slowPrint("等全部寫完我再一起放上來好了(陰暗地爬走)",2);  
}

void character_Mina(){
	cout<<endl;
	slowPrint("我中間劇情還沒寫完(尖叫)",2); 
	slowPrint("等全部寫完我再一起放上來好了(陰暗地爬走)",2);  
}

void character_Suoxiaohua(){
	cout<<endl;
	slowPrint("我中間劇情還沒寫完(尖叫)",2); 
	slowPrint("等全部寫完我再一起放上來好了(陰暗地爬走)",2); 
}

void character_Liuyiyi(){
	cout<<endl;
	slowPrint("我中間劇情還沒寫完(尖叫)",2); 
	slowPrint("等全部寫完我再一起放上來好了(陰暗地爬走)",2); 
}

void dialogue(int character){
	cout<<endl;
	slowPrint("你正準備翻到下一頁查看結果，卻冷不防地——「咚！」",3);
	slowPrint("一塊起司從書架上掉下來，直直砸在你頭上。",3); 
	cout<<endl;
	slowPrint("「嘶……」",1); 
	slowPrint("你忍不住倒吸一口氣，摸了摸頭，心想這下明天肯定會腫個包。",3); 
	slowPrint("還沒來得及抱怨，當你再次抬起頭時，一位不知何時出現的陌生人站在你面前。",3); 
	if(character==1) character_Bech();
	else if(character==2) character_Feichanyin();
	else if(character==3) character_Roselyne();
	else if(character==4) character_Iris();
	else if(character==5) character_Taro();
	else if(character==6) character_Siegfried();
	else if(character==7) character_Mina();
	else if(character==8) character_Suoxiaohua();
	else if(character==9) character_Liuyiyi();
}


void ending(){
	cout<<endl;
	slowPrint("你思考著接下來要說甚麼，此時一個念頭劃過腦海。",3);
	cout<<endl;
	slowPrint("「完了！！！我明天要交的報告 還！ 沒！ 做！！！」",1);
	cout<<endl;
	slowPrint("你不管三七二十一急忙衝去抓起起司。",3);
	slowPrint("途中你下意識地回過頭，與面前這位剛認識的陌生人對上眼。",3);
	cout<<endl;
	slowPrint("你眨了眨眼，算是無聲的道別，因為下一刻你的意識便被突如其來的眩暈帶走。",3);
	cout<<endl;
	slowPrint("…",3);
	slowPrint("……",3);
	slowPrint("………",3);
	cout<<endl;
	slowPrint("當你再度醒來時，面前的景色變回你熟悉的圖書館，也沒看到奇怪的人影。",3);
	slowPrint("你愣了一下，而後笑起來，決定把這段特別的經歷放在心裡，繼續未來的生活。",3);
	cout<<endl;
	slowPrint("〔The End〕",3);
}

int main() {
	HANDLE hOut = GetStdHandle(STD_OUTPUT_HANDLE);
    DWORD dwMode = 0;
    GetConsoleMode(hOut, &dwMode);
    dwMode |= ENABLE_VIRTUAL_TERMINAL_PROCESSING;
    SetConsoleMode(hOut, dwMode);
    
    
    opening();
    library_map();
    dialogue(test_text());
    ending();
    
    
    return 0;
}
//slowPrint("",3); 
//cout<<""<<endl;

