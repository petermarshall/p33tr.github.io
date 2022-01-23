---
layout: post
title: Mocking BeginTransation() in Dapper and MOQ
categories: [dotnet-core, code, tutorial, unit-test, TDD, MOQ]
---


Somewhere deep in your data access code you have.


``` csharp
            using (var transaction = _unitOfWork.DbConnection.BeginTransaction())
            {
                await _unitOfWork.SomeRepository.Delete(id).ConfigureAwait(false);

                await _unitOfWork.SomeRepository.DeleteSomethingElse(id).ConfigureAwait(false);

                transaction.Commit();
            }
```
The details in the transaction are not important. Mocking the Delete and the DeleteSomethingElse method is easy enough, but its wrapped in a transaction. So how do we get to mock that using MOQ in our unit test?

``` csharp
    var mockUnitOfWork = new Mock<IUnitOfWork>();
    mockUnitOfWork.Setup(uow => uow.DbConnection.BeginTransaction())
                    .Returns(new Mock<DbTransaction>().Object);
```
