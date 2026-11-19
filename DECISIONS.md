# DECISIONS

Answer **yourself**, in your own words, in 3–5 sentences per question.
Refer to concrete files and classes from your own repo (for example `Features/Bookings/CreateBooking/...`).
An unanswered question gives 0 points.

## 1. A rule you moved
Pick one business rule (R1–R8). In which file did it live in the starter and in which file does it live now? Why there?

Am ales regula R7 In starter era in Services/BookingService.cs (Create), dar avea bug: folosea <= (bloca si intervalele care doar se ating) si nu ignora rezervarile anulate. Acum am mutat-o in Domain/Bookings/Booking.cs
## 2. Result instead of an exception
Why is `Result` a better choice than an exception for "the room is occupied"? What happens in your code when an `Error` is returned?

Faptul ca o camera e ocupata e un caz normal de business, nu o eroare de sistem, deci nu are sens sa arunc exceptii pentru asta. cu acest Result se vede direct din semnatura metodei ca poate esua si nu mai am nevoie de middleware pentru exceptii. Concret, Room.Book intoarce BookingErrors.RoomOccupied , CreateBookingHandler verifica IsFailure

## 3. Who knows about EF Core?
Which classes in your project know about EF Core and which do not? What do you gain and what do you pay with Repository + Unit of Work?

De EF Core stiu doar clasele din `Infrastructure/Persistence`: `RoomHubDbContext`, `RoomRepository`, `BookingRepository`, `UnitOfWork` si `PersistenceExtensions`. Domeniul (`Room`, `Booking`) si handlerele din `Features/` lucreaza doar cu interfetele `IRoomRepository`, `IBookingRepository` si `IUnitOfWork` din `Domain/`. Castig ca domeniul nu depinde de baza de date, pot schimba SQLite cu altceva sau pot face teste cu repository-uri fake, iar salvarea se face o singura data prin `SaveChangesAsync`. Platesc cu mai multe fisiere si interfete, si uneori trebuie sa adaug metode noi in repository (cum a fost `GetActiveForPersonAsync` pentru R9).

## 4. The flow of a request
Draw in text (ASCII arrows) the path of a `POST /api/rooms/{roomId}/bookings` request through your project, with the **real** names of files and classes, from the endpoint to the database.

```
POST /api/rooms/{roomId}/bookings
  -> Features/Bookings/CreateBooking/CreateBookingEndpoint.cs (CreateBookingRequest -> CreateBookingCommand)
  -> Features/Bookings/CreateBooking/CreateBookingHandler.cs
       -> IRoomRepository.GetByIdAsync (RoomRepository)          -> null => RoomErrors.NotFound -> 404
       -> IBookingRepository.GetActiveForRoomAsync (BookingRepository)
       -> IBookingRepository.GetActiveForPersonAsync (BookingRepository)
       -> TimeProvider.GetUtcNow()
       -> Domain/Rooms/Room.cs  Room.Book(...)
            -> Domain/Bookings/Booking.cs  Booking.Create(...)    (R2, R3, R4, R5, R6)
            -> capacitate (R4), 2h pt camere mari (R10), OverlapsWith (R7), max 2/zi (R9)
            -> eroare => Result cu Error -> ToProblem() -> 400 / 409
       -> IBookingRepository.Add
       -> IUnitOfWork.SaveChangesAsync (UnitOfWork)
  -> Infrastructure/Persistence/RoomHubDbContext.cs -> SQLite (roomhub.db)
  <- 201 Created + Location /api/bookings/{id}
```

## 5. What you rejected from what the AI gave you
Give a concrete example of code or an idea proposed by the AI that you corrected or rejected, and say why.

AI-ul mi-a dat tot codul refactorizat, ,  cu cererile din `roomhub.http`. La R9 AI-ul a lasat comparatia pe `bookedBy` exacta (case-sensitive) si mi-a propus sa o fac fara majuscule, dar am lasat-o exacta pentru ca in cerinta scrie "same bookedBy".

## 6. If you had 30 more minutes
What would you improve and what risk did you leave in the project?

sincer nu stiu ce as putea sa dau improve.