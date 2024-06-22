### Quiz : Deviner le Pod qui n'est pas créé par un ReplicaSet

**Instructions :** Parmi les Pods listés ci-dessous, identifiez celui qui n'a pas été créé par un ReplicaSet.

**Question 1 :**  
Quel Pod n'a pas été créé par un ReplicaSet ?
- a) `my-app-1234567890-abcde`
- b) `frontend-8976543210-zxcvb`
- c) `backend-9876543210-qwert`
- d) `control-plane`

**Question 2 :**  
Quel Pod n'a pas été créé par un ReplicaSet ?
- a) `database-1122334455-asdfg`
- b) `cache-4433221100-poiu`
- c) `admin-9988776655-hjkl`
- d) `management`

**Question 3 :**  
Quel Pod n'a pas été créé par un ReplicaSet ?
- a) `service-5566778899-lkjh`
- b) `api-server-3344556677-mnbv`
- c) `data-123443211234-vcxz`
- d) `scheduler`

**Question 4 :**  
Quel Pod n'a pas été créé par un ReplicaSet ?
- a) `database-1122334455-asdfg`
- b) `database-2233445566-asdfg`
- c) `database-3344556677-zxcvb`
- d) `database-1122334455-qwert`
- e) `control-plane`

### Réponses

1. d) `control-plane`  
**Explication :** Les Pods créés par un ReplicaSet ont souvent des noms qui suivent le pattern `nom-du-pod-chaine-aléatoire`, ce qui n'est pas le cas de `control-plane`.

2. d) `management`  
**Explication :** De même, `management` ne suit pas le pattern typique des Pods créés par un ReplicaSet.

3. d) `scheduler`  
**Explication :** `scheduler` ne suit pas le pattern typique des Pods créés par un ReplicaSet.

4. e) `control-plane`  
**Explication :** Les autres Pods (`database-1122334455-asdfg`, `database-2233445566-asdfg`, `database-3344556677-zxcvb`, `database-1122334455-qwert`) suivent tous le pattern typique des Pods créés par un ReplicaSet, tandis que `control-plane` ne le suit pas.
