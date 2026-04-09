# EasyToSay
Hard to set out.
(just forget my poor English   OvO)

  
### **Mixed Version**
半路被钓去一个竞赛项目，然而组里已经把项目最亮眼的模型设计完毕了。Alright, after all, I wouldn't take part in the project without the invitation.  
我选择了云端数据库的部署（不知道这个词用对没），倒不是说它简单，单纯是上学期学了数据库这门课没有个实操经历。然后，就是很抓马的building journey了。  

---
1. 云端数据库，肯定要先选数据库啦... NO, I didn't suppose so at that time. Instead, I reviewed the SQL to begin with, spending 1.5 days on **LeetCode**... and totally forgetting what did matter.
2. 然后作为一个小卡拉米student，周中被学业包了，于是只能用可怜的周末时间来赶【And it just so happens to be Qingming Festival, a traditional Chinese festival.】。啊我的假期！Sorry 跑题了，太久没记录了orz
3. 像寒假打美赛那样，我开始了“*看起来很简单实则也很简单但小卡拉米还是艰难地磨蹭做了2天*”。谁懂，拖成啥样了，本来还有个Java小项目想写完来着...
4. 在组长建议下采用了云端一体式托管服务，问了AI选什么云端，可惜可能因为训练数据太过久远，AI给出来的回答并不完全准确和全面，兜兜转转采取了组长推荐的xxx cloud，然实际卡得不行，遂又开始找寻，但因为过程极为无聊和摸不着头脑，我的大脑已经自动忘记我到底是从哪儿发现的**Supabase**了😬
5. 在经过第一天从头到尾阅读了项目文档(虽然是AI出来的超长文档)，找云库，时间来到第二天 ———— **CREATE TABLE** ~ 最最抓马的一刻来了：**Supabase**提供了GUI，一个老练的程序员用了都说高效的操作，那我请问上周花了1.5天复习SQL算我很勤劳吗？（lol，just kidding, I’ve learned more than I did last term. 果然人还是得在做中学，就是这次云端全靠自己摸索😂）
6. 不过嘞，我可是用了SQL Editor做了一个TRIGGER whit a FUNCTION哦，not completely through GUI. 还不错吧，总算体现了我是学过数据库的哈哈（就是好像如果真的想大展身手，本地部署才会更加涉及数据库一点，至少是SQLite）
7. 在此，if you read right here, I recommend **Supabase** to you, which has RLS(Row Level Security, allowing user's query safer) and SDK(Software Development Kit, using Python library "supabase" to CRUD directly), to name a few.
8. 经过一通操作，其实 *cloud database* 也没有什么很复杂的，就是放在云服务器上的数据库，同步更方便，包括平台还会提供一些额外的服务/福利。

---
题外话：我发现RLS可以直接用于建的某一张表，但是按照项目总文档，是需要再建一张表来配合移动端。所以，后续聊。  
【2026.4.5 清明】

---
你好，我又更新了【原来这是连续剧吗Doge】  
事情是这样的，我问组长URL和KEY怎么给后端和移动端他们，组长说上传到`README.md`就行。为什么这事儿我会问组长呢，就是因为`service_role_key` **is absolutely secret**，但是我又想组长肯定“身经百战”，就没多问，但事实证明，**虽然确实不能把别人当傻子，但可以把自己当傻子呀，有啥跟自己认知不符的，直接问就行，而不是xxx一定是对的。**  
于是没错，大概三四个小时就这么又过去了。不过没关系，和上次相比，我又会用`git`CIL而不只是UIL操作，and 知道了原来Supabase的新版KEY可以和旧版KEY一样的使用，原来API KEY是根据JWT KEY生成的，并且API KEY只是相当于一个凭证/“证书”，具体的key是需要通过JWT KEY计算得到的。按理来说，revoke and 转换（Sorry 具体的单词忘记了） JWT KEY之后API KEY就能使用了，但因为还是不算很了解这个加密解密原理，故而保险起见，就重新生成了一个数据库，然后幸好幸好，**论工作留痕和代码复现的优越性**，果然像之前数据库老师说的，学了这门课不可能只是会点向导生成数据库吧，事实证明，有这个代码不要快太多🥹 感谢四天前的v-she。  
然后我也体会到了**API的魅力**，这种分层代码简直不要太爽，不管内部改动再大，接口不变，连接就不变，爱了爱了。  
【2026.4.9】
