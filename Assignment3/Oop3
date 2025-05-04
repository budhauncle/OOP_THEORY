#include <iostream>
#include <stdexcept>
#include <string>
#include <vector>
#include <fstream>

using namespace std;

#define MAX_SEATS 60

class PaymentIncompleteException : public exception
{
    const char *what() const noexcept override
    {
        return "Booking denied: user payment is not confirmed.";
    }
};

class SeatOccupiedException : public exception
{
    const char *what() const noexcept override
    {
        return "That seat has already been taken.";
    }
};

class RoleMismatchException : public exception
{
    const char *what() const noexcept override
    {
        return "Role conflict: cannot assign seat to this user category.";
    }
};

class EntityMissingException : public exception
{
    const char *what() const noexcept override
    {
        return "Could not locate the required item in the system.";
    }
};

class Seat
{
public:
    int seatID;
    string designatedFor;
    bool reserved;

    Seat()
    {
        seatID = 0;
        designatedFor = "";
        reserved = false;
    }

    Seat(int id, const string &type)
    {
        seatID = id;
        designatedFor = type;
        reserved = false;
    }
};

class Route
{
public:
    string origin, destination;
    int distanceKm;

    Route() {}

    Route(string o, string d, int km)
    {
        origin = o;
        destination = d;
        distanceKm = km;
    }

    bool isExtendedRoute() const
    {
        return distanceKm > 20;
    }
};

class Driver
{
public:
    string driverID, fullName, licenseID;

    Driver() {}

    Driver(string id, string name, string license)
    {
        driverID = id;
        fullName = name;
        licenseID = license;
    }
};

class User
{
public:
    string userID, name, email;
    bool paidStatus;

    User() {}

    User(string id, string n, string e, bool paid)
    {
        userID = id;
        name = n;
        email = e;
        paidStatus = paid;
    }

    virtual string getCategory() const = 0;
    virtual int computeFare(bool acAvailable) const = 0;
};

class Student : public User
{
public:
    Student() {}

    Student(string id, string n, string e, bool paid) : User(id, n, e, paid) {}

    string getCategory() const override
    {
        return "Student";
    }

    int computeFare(bool acAvailable) const override
    {
        return acAvailable ? 7200 : 5200;
    }
};

class Faculty : public User
{
public:
    Faculty() {}

    Faculty(string id, string n, string e, bool paid) : User(id, n, e, paid) {}

    string getCategory() const override
    {
        return "Faculty";
    }

    int computeFare(bool acAvailable) const override
    {
        return acAvailable ? 4800 : 3100;
    }
};

class Vehicle
{
public:
    string regNo, modelType;
    bool acEnabled;
    vector<Seat> seatList;
    Route *linkedRoute;
    Driver *assignedDriver;

    Vehicle()
    {
        linkedRoute = nullptr;
        assignedDriver = nullptr;
    }

    Vehicle(string reg, string model, bool ac, int seatTotal, string allowedType)
    {
        regNo = reg;
        modelType = model;
        acEnabled = ac;
        linkedRoute = nullptr;
        assignedDriver = nullptr;

        for (int i = 1; i <= seatTotal; ++i)
        {
            seatList.emplace_back(i, allowedType);
        }
    }

    void setRoute(Route *r)
    {
        linkedRoute = r;
    }

    void setDriver(Driver *d)
    {
        assignedDriver = d;
    }

    void allocateSeat(User *u, int seatNo)
    {
        if (!u->paidStatus)
        {
            throw PaymentIncompleteException();
        }

        if (seatNo < 1 || seatNo > seatList.size())
        {
            throw out_of_range("Seat number is not within the valid range.");
        }

        Seat &chosenSeat = seatList[seatNo - 1];

        if (chosenSeat.reserved)
        {
            throw SeatOccupiedException();
        }

        if (chosenSeat.designatedFor != u->getCategory())
        {
            throw RoleMismatchException();
        }

        chosenSeat.reserved = true;

        cout << "\nConfirmation: Seat " << seatNo << " assigned to " << u->name << " on vehicle " << regNo;
    }
};

int main()
{
    Student stud1("S111", "Adeel Khan", "adeel@fast.edu.pk", true);
    Faculty fac1("F212", "Dr. Noman", "noman@fast.edu.pk", true);

    Route r1("Johar", "FAST-NUCES", 23);
    Driver dr1("DRV001", "Waseem Ahmed", "LZ12345");

    Vehicle v1("VH001", "Coaster", true, 32, "Student");
    v1.setRoute(&r1);
    v1.setDriver(&dr1);

    try
    {
        v1.allocateSeat(&stud1, 9);
        v1.allocateSeat(&fac1, 13);
    }
    catch (exception &e)
    {
        cout << "\nSystem Alert: " << e.what() << '\n';
    }

    return 0;
}
