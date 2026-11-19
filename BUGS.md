# BUGS

The starter contains behaviors that contradict `ASSIGNMENT.md`. Write here every discrepancy you found.
Fill it in yourself, in your own words. There is no required number: what counts is that they are real.

| Which rule from ASSIGNMENT.md is violated (R1–R8) | Where the problem was in the starter (file, approximate line) | How you discovered it (test, reading, AI) | What you changed |
|---|---|---|---|
| R7 (intervale care se ating) | `Services/BookingService.cs`, ~linia 28 (`<=` in loc de `<`) | citire cod + AI | `Booking.OverlapsWith` cu `<` strict |
| R7 / R8 (anulatele blocau intervalul) | `Services/BookingService.cs`, ~linia 26 (`foreach` pe toate rezervarile) | citire cod + AI | `OverlapsWith` verifica `IsActive`, repo ia doar active (`GetActiveForRoomAsync`) |
| R8 (a doua anulare nu dadea 409) | `Services/BookingService.cs`, ~linia 64 (`Cancel`) | AI + test in `roomhub.http` | `Booking.Cancel()` returneaza `AlreadyCancelled` (409) |
| R5 (lipsea verificarea "nu in trecut") | `Services/BookingService.cs`, `Create` (~liniile 17-24) | citire cod | verificare in `Booking.Create` cu ora din `TimeProvider` |
| R6 (lipsea limita de 4 ore) | `Services/BookingService.cs`, `Create` (~liniile 17-24) | citire cod | `Booking.MaxDuration` verificat in `Booking.Create` |
| Persistenta (datele se pierdeau la restart) | `Repositories/RoomRepository.cs`, `Repositories/BookingRepository.cs` (liste in memorie) | citire cod | EF Core + SQLite in `Infrastructure/Persistence` |
