1、不管有没有，查一行出来

```
select top(1) A.MajorID,A.MName from (
	select top(1) rtrim(MajorID) as MajorID,rtrim(MName) as MName,1 as o from MajorInfo where MajorID like 'L%%' and MajorInfo.UnitId='90'
	union
	select top(1) '' as MajorID,'' as MName,0 as o from SysEdulevel
)A order by A.o desc
```

