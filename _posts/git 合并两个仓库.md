���������ֿ⣬һ����gitbook��дһ��

һ���Ƿ���github�����������������Ҫ��������վ���������Ļ��ǲ���https://github.com/lindexi/lindexi.github.io�����������Ҫ��html���������ļ�û��ֱ��ת��ȥ���������ֲ���ʹ�õ��������ߣ���������������Լ���дһ�������Ǿ�����winMarkdown��win10����������Ѿ�������û��

���˻��ǻص����⣬���������git�ϲ�

������git bash ���ҵ�һ���ֿ⣬��Ϊ��Ҫ�ϲ��Ĳֿ�

```
cd �ֿ�
```

�����Ҫ�ϲ��ֿ�

```
#git remote add �ֿ� �ֿ������Զ�ֿ̲�
git remote add lindexi git@github.com:lindexi/lindexi.github.io.git
```
��Զ�ֿ̲����أ���ʱ����Ū���µ�

```
git branch lindexi
git checkout lindexi
git fetch lindexi
git merge lindexi/master
�����ͻ
git add .
git commit -m "�ϲ�"
git push lindexi lindexi:master
git checkout master
git merge lindexi
git branch -d lindexi
```



