# Early Grave

A package in Go to validate URL search query parameters and store
them in the request context.

# Pagination & sorting

Most of the time pagination and sorting are needed to show data in
a comfort way to the user.

```
import (
  ...
  "github.com/bshuster-repo/earlygrave"
)

var paginationAndSortingHandler = earlygrave.New(
  PaginationValidator(),
  SortValidator([]string{"name", "role"}),
  PaginationExtractor(earlygrave.Pagination{Limit: 20, Offset: 0}),
  SortExtractor("created_at", "DESC"),
)

func usersHandler(w http.ResponseWriter, r *http.Request) {
  nr, err := paginationAndSortingHandler(r)
  if err != nil {
    w.setHeader(http.InternalServerError)
    return
  }
  pagination, pCtxErr := earlygrave.GetPaginationContext(nr)
  if pCtxErr != nil {
    w.setHeader(http.InternalServerError)
    return
  }
  sort, sCtxErr := earlygrave.GetSortContext(nr)
  if sCtxErr != nil {
    w.setHeader(http.InternalServerError)
    return
  }
  if err := db.QueryRow(
    "SELECT name, role, title FROM users LIMIT ? OFFSET ? SORT BY ? ?",
    pagination.Limit,
    pagination.Offset,
    sort.Column,
    sort.Direction,
  ); err != nil {
    ...
  }
  ...
}
```

We created an handler called `paginationAndSortingHandler` that will take
the request and return a new request with pagination and sorting data stored
inside the request context.

Then using `earlygrave.GetPaginationContext` and `earlygrave.GetSortContext`
we can retrieve this information and use it in the query.

# Author

Boaz Shuster

# License

MIT.
