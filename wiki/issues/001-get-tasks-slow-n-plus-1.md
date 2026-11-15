# #1 [Bug] 할 일이 많을 때 GET /tasks 응답이 느림 (태그 N+1 쿼리)

- 링크: https://github.com/goodsosbva/task-keeper/issues/1
- 생성일: 2026-11-08
- 라벨: `bug`, `needs-triage`
- 상태: Open

## 요약

`app/services/task_service.py`의 `list_tasks`가 `db.query(Task).all()`로 할 일만 조회합니다.
`Task.tags`는 lazy 로딩(`lazy="select"`)이므로, 응답을 직렬화할 때 할 일마다 태그 쿼리가 한 번씩 더 실행됩니다.
할 일이 N개면 쿼리가 1 + N번 실행됩니다.

## 확인 방법

`scripts/seed.py`로 데이터를 넣고 `SQLALCHEMY_ECHO=1`로 서버를 띄운 뒤 `GET /tasks`를 호출합니다.
콘솔에서 tags SELECT가 할 일 개수만큼 반복되는지 확인합니다.

## 제안된 수정

```python
from sqlalchemy.orm import selectinload
tasks = db.query(Task).options(selectinload(Task.tags)).all()
```

## 작업 기록

- 저장소에 `needs-triage` 라벨이 없어 새로 만들었습니다(색상 `#fbca04`).
- 저장소의 Issues 기능이 꺼져 있어 `gh repo edit --enable-issues`로 켰습니다.
