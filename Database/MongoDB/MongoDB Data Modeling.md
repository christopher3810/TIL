
유연한 스키마를 제공하고, 다양한 데이터 모델링 기법을 허용함. 

Parent References, Child References, Array of Ancestors, Materialized Paths, Nested Sets 등이 있음.

### Parent References

이 방법은 각 Document가 부모의 id를 참조하는 방식.

Parent References는 "Parent - child" 관계에 유용함.

예를 들어, "댓글"이라는 문서가 "게시물"이라는 Parent Document를 참조할 수 있음.

```mongodb
Post
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a00"),
   title: "Post1",
   content: "This is Post1"
}

Comment
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a01"),
   content: "This is a comment on Post1",
   post_id: ObjectId("60f71c128e4f0d4b5c2e2a00")  // Referring to parent Post document
}
```


### Child References

Child References 는각 Parent Document가 Child Document들의 id를 참조하는 방식. 

Parent Document에서 Child를 쉽게 나열하고 싶을 때 유용.

```mongodb
Post
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a00"),
   title: "Post1",
   content: "This is Post1",
   comments: [ObjectId("60f71c128e4f0d4b5c2e2a01"), ObjectId("60f71c128e4f0d4b5c2e2a02")]  // Array of child comment _ids
}

```


### Arrays of Ancestors

이 방식은 각 document가 조상 document들의 id를 배열로 가지는 방식. 

이러한 방법은 계층적인 구조를 가진 데이터에 유용. 

예를 들어, 파일 시스템의 폴더 구조나 조직의 부서 구조를 표현할 때 사용할 수 있음.

```mongodb
Folder
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a00"),
   name: "Folder1",
   ancestors: []  // Root folder, so no ancestors
}

Subfolder
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a01"),
   name: "Subfolder1",
   ancestors: [ObjectId("60f71c128e4f0d4b5c2e2a00")]  // Ancestors array containing _id of parent 'Folder1'
}

```


### Materialized Paths

이 방법은 각 문서가 경로를 문자열로 가지는 방식. 

Materialized Paths는 계층적인 구조를 가진 데이터에 특히 유용하며, 트리 구조의 문서를 쉽게 쿼리할 수 있게 함.

```mongodb
Folder
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a00"),
   name: "Folder1",
   path: ",Folder1,"  // Materialized path representing its own name
}

Subfolder
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a01"),
   name: "Subfolder1",
   path: ",Folder1,Subfolder1,"  // Materialized path containing the names of the ancestor folders
}


```

### Nested Sets

이 모델은 트리 구조를 표현하는 데 유용하며, 트리의 모든 잎을 통과하는 하나의 지속 경로를 사용하여 트리를 표현. 

각 노드에는 "왼쪽" 및 "오른쪽" 경계를 나타내는 두 개의 숫자가 있음. 

Nested Sets 모델은 계층적인 데이터를 처리할 때 유용하지만, 데이터 구조를 변경하는 데는 비용이 많이 들 수 있음.

```mongodb
Folder
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a00"),
   name: "Folder1",
   path: ",Folder1,"  // Materialized path representing its own name
}

Subfolder
{
   _id: ObjectId("60f71c128e4f0d4b5c2e2a01"),
   name: "Subfolder1",
   path: ",Folder1,Subfolder1,"  // Materialized path containing the names of the ancestor folders
}

```