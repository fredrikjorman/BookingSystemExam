# Booking System – individual coding exam

A console application built with C# and .NET 10 for managing meeting room bookings.

## Features

* View active bookings sorted by date and start time.

* Search for bookings by customer name.

* Create new bookings.

* Cancel existing bookings.

* Validate booking times, room capacity and overlapping bookings.

* Handle invalid user input.

## Available Rooms

- Aspen – 4 people
- Birken – 8 people
- Eken – 12 people


## Requirements

- .NET 10 SDK

## Run the application

```bash
dotnet run --project src/BookingSystem
```

## Run the tests

```bash
dotnet test
```
The project includes 14 automated tests using xUnit.

## Debugging

See [DEBUGGING.md](DEBUGGING.md) for a description of a bug I fixed and how I tested the solution.
