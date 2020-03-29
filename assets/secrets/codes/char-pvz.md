---
layout: article
title: 这一切都要从一只蝙蝠说起
show_title: false
permalink: /codes/char-pvz
---

```cpp
#include<stdio.h>
#include<math.h>
#include<time.h>
#include<conio.h>
#include<windows.h>
#define UPDINT 200
#define INISUN 50

template<typename Tp>
class linked_list{
public:
  struct node{
    Tp val;bool exi;node *next;
  };
  int tot,poltop;
  node nodes[1<<20],*pool[1<<20],*now;
  void insert(Tp t){
    node *p=poltop?pool[poltop--]:nodes+(++tot);
    p->val=t,p->exi=1,p->next=now,now=p;
  }
  void remove(node *p,node *prev){
    if(now==p)now=p->next;
    if(prev)prev->next=p->next;
    pool[++poltop]=p,p->exi=0;
  }
};

HANDLE hout=GetStdHandle(STD_OUTPUT_HANDLE);
int flgcnt,wavcnt,timcnt,suncnt;
int chid,chx,chy;

struct sun_gener{
  int prod,inter,prev;
  bool turn(){
    return prev+inter<=timcnt&&prev+inter+UPDINT>timcnt;
  }
  void print();
  void update();
};
sun_gener sunG;
sun_gener sunGlib[]={
#include "sunGlib.data"
};

struct zombie_gener{
  int prod[20],range[20],inter,prev;
  bool turn(){
    return prev+inter<=timcnt&&prev+inter+UPDINT>timcnt;
  }
  void print();
  void update();
};
zombie_gener zmbG;
zombie_gener zmbGlib[]={
#include "zmbGlib.data"
};

struct card{
  char type;int typid,attr;
  int cooldown,cost,prev;
  bool operator<(card rhs)const{return typid<rhs.typid;}
  bool ready(){
    return prev+cooldown<=timcnt&&cost<=suncnt;
  }
  void print(int x,int y,bool chosen);
};
card cards[5][6];
card cardlib[]={
#include "cardlib.data"
};

struct lawn{
  char type[2];int typid,attr[2];
  int attack,slow,freeze,range,bulrange,prod,hit,inter,prev;
  bool dispos,single,trig,obstr;
  bool operator<(lawn rhs)const{return typid<rhs.typid;}
  bool ready(){return prev+inter<=timcnt;}
  bool turn(){
    return prev+inter<=timcnt&&prev+inter+UPDINT>timcnt;
  }
  void print(int x,int y,bool chosen);
  void update(int x,int y);
};
lawn lawnmap[9][5];
lawn emptlawn={ {0,0},-1};
lawn lawnlib[]={
#include "lawnlib.data"
};

struct bullet{
  char type;int typid,x,y,attr;
  int attack,slow,freeze,dest,inter,prev;
  bool single;
  bool operator<(bullet rhs)const{return typid<rhs.typid;}
  bool turn(){
    return prev+inter<=timcnt&&prev+inter+UPDINT>timcnt;
  }
  bool update();
};
typedef linked_list<bullet>::node bulnode;
linked_list<bullet> bullets;
bullet nobul={0,-1};
bullet bullib[]={
#include "bullib.data"
};

struct zombie{
  char type[3];int typid,x,y,attr[3];
  int attack,slow,freeze,skip,hit,point;
  int winter[3],binter,prev;
  bool proof[3],bulproof[3];
  bool operator<(zombie rhs)const{return typid<rhs.typid;}
  int state(){return skip?2:hit<=point;}
  bool wready(){return prev+winter[state()]<=timcnt;}
  bool bready(){return prev+binter<=timcnt;}
  bool sturn(){
    return timcnt<slow&&timcnt+UPDINT>=slow;
  }
  void update();
};
typedef linked_list<zombie>::node zmbnode;
linked_list<zombie> zombies;
zombie nozmb={ {0,0,0},-1};
zombie zmblib[]={
#include "zmblib.data"
};

int crdtot=sizeof(cardlib)/sizeof(card);
int zmbtot=sizeof(zmblib)/sizeof(zombie);

void sun_gener::print(){
  COORD pos={5,0};
  SetConsoleCursorPosition(hout,pos);
  SetConsoleTextAttribute(hout,78);
  printf("*");
  SetConsoleTextAttribute(hout,79);
  printf("%4d",suncnt<10000?suncnt:9999);
}
void sun_gener::update(){
  if(turn())prev=timcnt,suncnt+=prod;
}
void zombie_gener::print(){
  COORD pos={10,0};
  SetConsoleCursorPosition(hout,pos);
  SetConsoleTextAttribute(hout,110);
  printf("     flag: %d",flgcnt);
}
void zombie_gener::update(){
  if(turn()||!zombies.now&&(flgcnt||wavcnt)){
    int cprod=prod[flgcnt?19:wavcnt];
    int cr=flgcnt*5+range[wavcnt];
    if(cr>zmbtot)cr=zmbtot;
    prev=timcnt,wavcnt++;
    for(int i=0;i<cprod;i++){
      zombie zmb=zmblib[rand()%cr];
      zmb.y=rand()%5,zmb.prev=timcnt;
      zombies.insert(zmb);
    }
  }
}
void card::print(int x,int y,bool chosen){
  COORD pos={x*5,y};
  SetConsoleCursorPosition(hout,pos);
  SetConsoleTextAttribute(hout,ready()?attr|8:attr);
  printf("%c",type);
  SetConsoleTextAttribute(hout,chosen?176:0);
  printf(" ");
  SetConsoleTextAttribute(hout,ready()?240:112);
  printf("%3d",cost);
}
void lawn::print(int x,int y,bool chosen){
  COORD pos={x*2+6,y+1};
  SetConsoleCursorPosition(hout,pos);
  bullet bul1=nobul,bul2=nobul;
  for(bulnode *p=bullets.now;p;p=p->next){
    bullet &cbul=p->val;
    if(cbul.x==x*2&&cbul.y==y&&bul1<cbul)bul1=cbul;
    if(cbul.x==x*2+1&&cbul.y==y&&bul2<cbul)bul2=cbul;
  }
  zombie zmb1=nozmb,zmb2=nozmb;
  for(zmbnode *p=zombies.now;p;p=p->next){
    zombie &czmb=p->val;
    if(czmb.x==x*2&&czmb.y==y&&zmb1<czmb)zmb1=czmb;
    if(czmb.x==x*2+1&&czmb.y==y&&zmb2<czmb)zmb2=czmb;
  }
  int cattr;char c;
  int st=!ready(),zmbst=zmb1.state();
  if(~typid)cattr=attr[st]|32,c=type[st];
  else if(~zmb1.typid)
    cattr=zmb1.attr[zmbst]|32,c=zmb1.type[zmbst];
  else if(~bul1.typid)
    cattr=bul1.attr|32,c=bul1.type;
  else cattr=32,c=' ';
  SetConsoleTextAttribute(hout,cattr);
  printf("%c",c);
  int bkg=chosen?176:32;zmbst=zmb2.state();
  if(~zmb2.typid)
    cattr=zmb2.attr[zmbst]|bkg,c=zmb2.type[zmbst];
  else if(~bul2.typid)
    cattr=bul2.attr|bkg,c=bul2.type;
  else cattr=bkg,c=' ';
  SetConsoleTextAttribute(hout,cattr);
  printf("%c",c);
}
void lawn::update(int x,int y){
  if(!trig&&!turn()||trig&&!ready())return;
  bool f=!trig;
  for(zmbnode *p=zombies.now;p;p=p->next){
    zombie &czmb=p->val;
    if(czmb.x>=x*2&&czmb.x<=x*2+1&&czmb.y==y&&!czmb.skip){f=1;break;}
  }
  if(!f)return;
  prev=timcnt,suncnt+=prod;
  for(zmbnode *p=zombies.now,*q=0;p;p=p->next){
    zombie &czmb=p->val;
    if(abs(czmb.x-x*2)<=range*2+1&&abs(czmb.y-y)<=range){
      czmb.hit-=attack;
      if(slow&&!czmb.proof[czmb.state()]){
        if(czmb.slow<timcnt){
          for(int k=0;k<3;k++)czmb.winter[k]*=2;
          czmb.binter*=2;
        }
        czmb.slow=timcnt+slow;
      }
      if(freeze&&!czmb.proof[czmb.state()])
        czmb.freeze=timcnt+freeze;
      if(czmb.hit<=0)zombies.remove(p,q);
      if(single)break;
    }
    if(p->exi)q=p;
  }
  bullet bul=bullib[typid];
  if(bul.type)for(zmbnode *p=zombies.now;p;p=p->next){
    zombie &czmb=p->val;
    if(czmb.x>=x*2&&czmb.x<=(x+bulrange)*2+1&&czmb.y==y){
      bul.x=x*2,bul.y=y,bul.dest=(x+bulrange)*2+1;
      bul.prev=timcnt;
      bullets.insert(bul);break;
    }
  }
  if(dispos)*this=emptlawn;
}
bool bullet::update(){
  if(turn())x++,prev=timcnt;
  if(x>17||x>dest)return 1;
  for(zmbnode *p=zombies.now,*q=0;p;p=p->next){
    zombie &czmb=p->val;
    if(x==czmb.x&&y==czmb.y){
      czmb.hit-=attack;
      if(slow&&!czmb.bulproof[czmb.state()]){
        if(czmb.slow<timcnt){
          for(int k=0;k<3;k++)czmb.winter[k]*=2;
          czmb.binter*=2;
        }
        czmb.slow=timcnt+slow;
      }
      if(freeze&&!czmb.bulproof[czmb.state()])
        czmb.freeze=timcnt+freeze;
      if(czmb.hit<=0)zombies.remove(p,q);
      if(single)return 1;
    }
    if(p->exi)q=p;
  }
}
void zombie::update(){
  lawn &pos=lawnmap[x/2][y];
  if(pos.obstr)skip=0;
  if(freeze<timcnt){
    if(!skip){
      if(~pos.typid&&bready()){
        pos.hit-=attack,prev=timcnt;
        if(pos.hit<=0)pos=emptlawn;
      }
      if(!~pos.typid&&wready())x--,prev=timcnt;
    }
    else if(wready()){
      x--,prev=timcnt;
      if(~pos.typid)skip--,x-=!(x&1);
    }
  }
  if(sturn()){
    for(int k=0;k<3;k++)winter[k]/=2;
    binter/=2;
  }
}

void print_background(){
  COORD pos;
  for(int i=0;i<6;i++){
    pos.X=5,pos.Y=i;
    SetConsoleCursorPosition(hout,pos);
    SetConsoleTextAttribute(hout,96);
    printf("                    ");
  }
  pos.X=11,pos.Y=0;
  SetConsoleCursorPosition(hout,pos);
  SetConsoleTextAttribute(hout,72);
  printf("+");
  SetConsoleTextAttribute(hout,76);
  printf("-");
}
void init_console(){
  SetConsoleTitle("char pvz");
  COORD size={25,6};
  SetConsoleScreenBufferSize(hout,size);
  SMALL_RECT rect={0,0,24,5};
  SetConsoleWindowInfo(hout,1,&rect);
  CONSOLE_CURSOR_INFO cursor_info={1,0};
  SetConsoleCursorInfo(hout,&cursor_info);
}
void init_game(){
  sunG=sunGlib[0],zmbG=zmbGlib[0],suncnt=INISUN;
  bullets.tot=bullets.poltop=0,bullets.now=0;
  zombies.tot=zombies.poltop=0,zombies.now=0;
  for(int i=0;i<5;i++)
    for(int j=0;j<6;j++)
      if(i*6+j<crdtot)cards[i][j]=cardlib[i*6+j];
  for(int i=0;i<9;i++)
    for(int j=0;j<5;j++)lawnmap[i][j]=emptlawn;
}
int stop_game(){
  COORD pos={7,2};
  SetConsoleCursorPosition(hout,pos);
  SetConsoleTextAttribute(hout,15);
  printf("GAME PAUSED");
  for(;;)if(kbhit())switch(getch()){
  case 27:return 0;
  case 32:return 1;
  }
}
int judge_game(){
  for(zmbnode *p=zombies.now;p;p=p->next){
    zombie &czmb=p->val;
    if(czmb.x>=0)continue;
    COORD pos={czmb.x+6,czmb.y+1};
    SetConsoleCursorPosition(hout,pos);
    int cattr=czmb.attr[czmb.state()]|96;
    SetConsoleTextAttribute(hout,cattr);
    printf("%c",czmb.type[czmb.state()]);
    pos.X=1,pos.Y=2;
    SetConsoleCursorPosition(hout,pos);
    SetConsoleTextAttribute(hout,10);
    printf("ZOMBIES ATE YOUR BRAIN!");
    for(;;)if(kbhit())switch(getch()){
    case 27:return 0;
    case 8:return 2;
    }
  }
  return 1;
}
int update_card(){
  if(kbhit())switch(getch()){
  case 27:return 0;
  case 8:return 2;
  case 32:return 3;
  case 119:if(chid)chid--;break;
  case 115:if(chid<5&&chid+1<crdtot)chid++;break;
  case 224:
    switch(getch()){
    case 72:if(chy)chy--;break;
    case 80:if(chy<5&&chx*6+chy+1<crdtot)chy++;break;
    case 75:if(chx>1)chx--;break;
    case 77:if(chx<4&&chx*6+chy+6<crdtot)chx++;break;
    }
    break;
  case 13:
    if(chx*6+chy<crdtot){
      card tmp=cards[0][chid];
      cards[0][chid]=cards[chx][chy],cards[chx][chy]=tmp;
    }
    break;
  }
  for(int i=0;i<5;i++)
    for(int j=0;j<6;j++)if(i*6+j<crdtot){
      bool chosen=!i&&chid==j||chx==i&&chy==j;
      cards[i][j].print(i,j,chosen);
  }
  return 1;
}
int update_game(){
  if(kbhit())switch(getch()){
  case 27:return 0;
  case 8:return 2;
  case 32:if(!stop_game())return 0;break;
  case 119:if(chid)chid--;break;
  case 115:if(chid<5&&chid+1<crdtot)chid++;break;
  case 224:
    switch(getch()){
    case 72:if(chy)chy--;break;
    case 80:if(chy<4)chy++;break;
    case 75:if(chx)chx--;break;
    case 77:if(chx<8)chx++;break;
    }
    break;
  case 13:
    if(cards[0][chid].ready()&&!~lawnmap[chx][chy].typid){
      int cid=cards[0][chid].typid;
      lawnmap[chx][chy]=lawnlib[cid];
      cards[0][chid].prev=timcnt;
      lawnmap[chx][chy].prev=timcnt+lawnlib[cid].prev;
      suncnt-=cards[0][chid].cost;
    }
    break;
  case 45:lawnmap[chx][chy]=emptlawn;break;
  }
  sunG.update();
  if(wavcnt<19)zmbG.update();
  else if(!zombies.now)return 3;
  for(int i=0;i<9;i++)
    for(int j=0;j<5;j++)lawnmap[i][j].update(i,j);
  for(bulnode *p=bullets.now,*q=0;p;p=p->next){
    bullet &cbul=p->val;
    if(cbul.update())bullets.remove(p,q);
    if(p->exi)q=p;
  }
  for(zmbnode *p=zombies.now,*q=0;p;p=p->next){
    zombie &czmb=p->val;
    czmb.update();
    if(p->exi)q=p;
  }
  sunG.print();
  zmbG.print();
  for(int i=0;i<6&&i<crdtot;i++)
    cards[0][i].print(0,i,chid==i);
  for(int i=0;i<9;i++)
    for(int j=0;j<5;j++)
      lawnmap[i][j].print(i,j,chx==i&&chy==j);
  timcnt+=UPDINT;
  return judge_game();
}
int choose_card(){
  print_background();
  sunG.print();
  zmbG.print();
  for(int i=0;i<9;i++)
    for(int j=0;j<5;j++)lawnmap[i][j].print(i,j,0);
  for(;;){
    int ret=update_card();
    if(ret==3)return 1;
    if(ret!=1)return ret;
  }
}
int start_game(){
  print_background();
  wavcnt=0;
  for(;;){
    int ret=update_game();
    if(ret==3)return 1;
    if(ret!=1)return ret;
  }
}
int run_game(){
  int ret;timcnt=UPDINT;
  for(flgcnt=0;;flgcnt++){
    chid=chy=0,chx=1,ret=choose_card();
    if(ret!=1)return ret;
    chid=chx=chy=0,ret=start_game();
    if(ret!=1)return ret;
  }
}
int main(){
  srand(time(0));
  init_console();
  do init_game();while(run_game());
  return 0;
}
```
