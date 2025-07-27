# 🧠 복습 예정 카드 대시보드

```dataview
table review-due as "📅 복습 예정일", review-last-reviewed as "✅ 마지막 복습", file.link as "📘 주제"
from #flashcards
where review-due <= date(today)
sort review-due asc
```

