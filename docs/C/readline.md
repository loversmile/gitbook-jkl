#if 1
/* GNU Readline是一个跨平台开源程序库，提供交互式的文本编辑功能。应用程序借助该库函数，*/
/*允许用户编辑键入的命令行，并提供自动补全和命令历史等功能。Bash(Bourne Again Shell)、GDB/*
/*tp 和mail等程序就使用Readline库提供其命令行界面。*/




/*主要函数*/


char *readline(const char *prompt);
/*该函数打印参数prompt 所指的提示字符串，然后读取并返回用户输入的单行文本(剔除换行符)。若prompt*/
/*为空指针或指向空字符串，则不显示任何提示。若尚未读取到字符就遇到EOF，则该函数返回NULL；否则返*/
/*回由malloc()分配的命令行内存，故调用结束后应通过free()显式地释放内存。*/


void add_history(const char *string);
/*读取命令行后，可调用add_history()函数将该行存入命令行历史列表中，以便后续重取。*/


 
typedef char *rl_compentry_func_t(const char *text, int state);
char **rl_completion_matches(const char *text, rl_compentry_func_t *entry_func);
/*rl_completion_matches()函数用于自动补全：*/
/*该函数返回一个字符串数组，即参数text的补全列表；若没有补全项，则函数返回NULL。该数组以空指针结尾，*/
/*首个条目将替换text，其余条目为可能的补全项。函数指针entry_func指向一个具有两个参数的函数。*/
/*其中参数state在首次调用时为零，后续调用时为非零。未匹配到text时，entry_func返回空指针。*/


rl_compentry_func_t *rl_completion_entry_function;
/* 函数指针rl_completion_entry_function指向rl_completion_matches()所使用的补全生成函数：*/
/*若其值为空，则使用默认的文件名补全生成函数，即rl_filename_completion_function()。*/


typedef char **rl_completion_func_t(const char *text, int start, int end);
rl_completion_func_t * rl_attempted_completion_function;
/*应用程序可通过函数指针rl_attempted_completion_function自定义补全函数：*/
/*该变量指向创建匹配补全的替代函数。函数参数 start和 end为字符串缓冲区rl_line_buffer的下标，以定义*/
/*参数text的边界。若该函数存在且返回NULL，或者该变量值被设置为NULL，则rl_complete()将调用*/
/*rl_completion_entry_function指向的函数来生成匹配结果；除此之外，程序使用该变量所指函数返回的字符串数组。*/
/*若该变量所指函数将变量rl_attempted_completion_over设置为非零值，则Readline将不执行默认的文件名补全*/
/*(即使自定义补全函数匹配失败)。*/


/*读取命令行，执行相关命令，支持tab补全功能及历史，命令记录，不包含复杂命令行的解析*/
#endif


#include <stdio.h>
#include <malloc.h>
#include <string.h>
#include <readline/readline.h>
#include <readline/history.h>


int showAllCmd(void);
/*实现具体的命令回调函数*/
int showInfo(void)
{
    printf("%s\n",__FUNCTION__);
    return 0;
}


int setLogLevel(void)
{
    printf("%s\n",__FUNCTION__);
    return 0;
}


int testBatch(void)
{
    printf("%s\n",__FUNCTION__);
    return 0;
}


int testEndianOper(void)
{
    printf("%s\n",__FUNCTION__);
    return 0;
}


int testEndOper(void)
{
    printf("%s\n",__FUNCTION__);
    return 0;
}


typedef int (*cmdFunc)(void);
typedef struct tcmdPro
{
    char* cmdStr;
    cmdFunc cmdFunc;
    char* cmdDetails;
    
}cmdPro;


//命令表
static cmdPro gCmdMap[] = {
    {"showMeInfo",       showInfo,       "show info"},
    {"setLogCtrl",       setLogLevel,    "setLogLevel"},
    {"testBatch ",        testBatch,     "testBatch"},
    {"testEndian",       testEndianOper, "testEndianOper"},
    {"testEnd",          testEndOper,    "testEndOper"},
    {"?",                showAllCmd,     "showAllCmd"},


    {NULL,NULL,NULL}
};


#define CMD_MAP_NUM     ((sizeof(gCmdMap)/sizeof(cmdPro)) - 1)/*End*/




int showAllCmd(void)
{
    unsigned char i = 0;
    for(i = 0;i < CMD_MAP_NUM;i++)
    {
        printf("%-25s       %s\n",gCmdMap[i].cmdStr,gCmdMap[i].cmdDetails);
    }
    return 0;
}


static char pszCmdPrompt[] = "<netopeer-cli>";


//返回gCmdMap中的CmdStr列(必须为只读字符串)，以供CmdGenerator使用
static char *getCmdByIndex(unsigned int CmdIndex)
{
  if(CmdIndex >=  CMD_MAP_NUM)
      return NULL;
  return gCmdMap[CmdIndex].cmdStr;
}
 
static char *cmdGenerator(const char *pszText, int State)
{
 static int ListIdx = 0, TextLen = 0;
  if(!State)
  {
      ListIdx = 0;
      TextLen = strlen(pszText);
  }


  //当输入字符串与命令列表中某命令部分匹配时，返回该命令字符串
  const char *pszName = NULL;
  while((pszName = getCmdByIndex(ListIdx)))
  {
      ListIdx++;


      if(!strncmp (pszName, pszText, TextLen))
          return strdup(pszName);
  }


  return NULL;
}
 
static char **cmdCompletion (const char *pszText, int Start, int End)
{
  char **pMatches = NULL;
  if(0 == Start)
      pMatches = rl_completion_matches(pszText, cmdGenerator);


  return pMatches;
}


static char *writeSpaceHandle(char* str)
{
    unsigned char i = 0;
    char* strHead = NULL;
    
    if(str == NULL)
    {
        printf("the str is noexist\n");
        return NULL;
    }
    
    for (i = 0; str[i] && whitespace (str[i]); i++);
strHead = str + i;
        
    return  strHead;   
}


int readLineInit(void)
{
    /*初始化tab补全*/
    rl_attempted_completion_function = cmdCompletion;
}


char *readCmdLine()
{
    char *pszLineRead = NULL;  //终端输入字符串
    char *pszStripLine = NULL;
    
    //读取用户输入的命令行
    pszLineRead = readline(pszCmdPrompt);
    //处理换行符,readline解析为空字符串
    if(!strcmp(pszLineRead,""))
    {
        return NULL;
    }
    //剔除命令行首尾的空白字符。若剔除后的命令不为空，则存入历史列表
    pszStripLine = writeSpaceHandle(pszLineRead);


    return pszStripLine;
}


static int isQuitCmd(char* cmdStr)
{
     if((!strcmp(cmdStr,"quit")) || (!strcmp(cmdStr,"exit")))
     {
        return 0;
     }
     return 1;
}


static int cmdHandle(char* cmdStr)
{
    unsigned char i = 0;
    for(i = 0;i < CMD_MAP_NUM;i++)
    {
        if(!strncmp(cmdStr,gCmdMap[i].cmdStr,strlen(gCmdMap[i].cmdStr)))
        {
            gCmdMap[i].cmdFunc();
            return 0;
        }
    }
    return -1;
}


int main(void)
{
    /*初始化界面提示*/
    printf("This is a command line using readline libary!\n");
    printf("Input quit or exit to quit!\n");
    /*初始化readline*/
    readLineInit();
    while(1)
    {
        char *cmdStr = NULL;
        /*读取命令*/
        cmdStr = readCmdLine();
        /*执行命令*/
        if(cmdStr == NULL)
        {
            continue;
        }
        /*退出命令*/
        if(!isQuitCmd(cmdStr))
        {
            free(cmdStr);
            break;
        }
        /*其他有效命令*/
        if(cmdHandle(cmdStr) == -1)
        {
            printf("cmd invaild\n");
        }
        else
        {
            /*保存有效命令*/
            add_history(cmdStr); 
        }
        free(cmdStr);
    }
    /**/
    return;
}
--------------------- 
作者：徐维溢 
来源：CSDN 
原文：https://blog.csdn.net/u014600842/article/details/73920825 
版权声明：本文为博主原创文章，转载请附上博文链接！