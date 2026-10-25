# Сценарій 2. Успішне бронювання столика

**Мета:** створити бронювання для зареєстрованого користувача та ініціювати надсилання листа-підтвердження.

**Учасники:**

- Зареєстрований користувач - обирає столик, указує параметри та підтверджує бронювання.

- ReservationController - обробляє запит, перевіряє параметри та координує створення бронювання.

- Table - надає інформацію про столики.

- Reservation - перевіряє наявні бронювання та зберігає нове бронювання.

- Email Service - надсилає лист-підтвердження користувачу.

- EmailConfirmation - зберігає результат надсилання листа.

**Послідовність дій:**

1. Користувач надсилає до ReservationController запит на пошук столиків, указуючи дату, час і кількість людей.

2. ReservationCOntroller перевіряє коректність параметрів пошуку.

3. Якщо параметри коректні, ReservationController надсилає до Table запит на отримання столиків із достатньою місткістю.

4. Table повертає список столиків, які підходять за кількістю гостей.

5. ReservationController надсилає запит до Reservation на отримання бронювань на вибрані дату й час.

6. Reservation повертає список уже забрьоньованих столиків.

7. ReservationController відбирає доступні столики та показує їх користувачу.

8. Користувач обирає столик зі списку та надсилає до ReservationController запит на підтвердження бронювання.

9. ReservationController повторно перевіряє доступність вибраного столика через Reservation.

10. Reservation повертає результат перевірки. Якщо столик доступний, ReservationСontroller надсилає запит на створення бронювання.

11. Reservation зберігає бронювання в базі даних і повертає підтвердження успішного збереження.

12. ReservationController передає до Email Service запит на надсилання листа-підтвердження з даними бронювання та електронною адресою користувача.

13. Email Service повертає результат надсилання листа.

14. Система записує результат надсилання листа.

15. ReservationController повідомляє користувача про успішне створення бронювання.

Sequence-діаграма:

```mermaid
sequenceDiagram
    actor User as Зареєстрований користувач
    participant RC as ReservationController
    participant T as Table
    participant R as Reservation
    participant ES as Email Service
    participant EC as EmailConfirmation

    activate User
    User->>RC: searchAvailableTables(date, time, guests)
    activate RC

    RC->>RC: validateParameters(date, time, guests)

    alt Некоректні параметри
        RC-->>User: returnValidationError()
    else Параметри коректні
        RC->>T: getTablesByCapacity(guests)
        activate T
        T-->>RC: returnSuitableTables()
        deactivate T

        RC->>R: findUnavailableTables(date, time)
        activate R
        R-->>RC: returnUnavailableTables()
        deactivate R

        RC->>RC: filterAvailableTables()

        alt Доступних столиків немає
            RC-->>User: displayNoAvailableTables()
        else Доступні столики знайдено
            RC-->>User: displayAvailableTables(tables)
            User->>RC: confirmReservation(tableId, guests)
            RC->>RC: validateGuestCapacity(guests, tableId)

            RC->>R: checkAvailability(tableId, date, time)
            activate R
            R-->>RC: returnAvailabilityResult()

            alt Столик уже зайнятий
                RC-->>User: displayTableUnavailable()
            else Столик доступний
                RC->>R: createReservation(userId, tableId, date, time, guests)
                R->>R: saveReservation()
                R-->>RC: returnSavedReservation(reservation)

                RC->>ES: sendConfirmation(reservation, email)
                activate ES
                ES-->>RC: returnEmailResult(result)
                deactivate ES

                RC->>EC: saveEmailResult(reservationId, result)
                activate EC
                EC-->>RC: confirmEmailResultSaved()
                deactivate EC

                RC-->>User: displayReservationSuccess()
            end
            deactivate R
        end
    end

    deactivate RC
    deactivate User
```
