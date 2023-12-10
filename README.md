# c-projrct
#include <iostream>
#include <vector>
#include <string>
#include <ctime>
#include <algorithm>

using namespace std;

class Event {
public:
    string name;
    string date;
    string time;

    Event(const string& eventName, const string& eventDate, const string& eventTime)
        : name(eventName), date(eventDate), time(eventTime) {}
};

class Calendar {
private:
    int currentMonth;
    int currentYear;
    vector<Event> events;

public:
    Calendar() {
        time_t now = time(0);
        tm* ltm = localtime(&now);
        currentMonth = 1 + ltm->tm_mon;
        currentYear = 1900 + ltm->tm_year;
    }

    void display() {
        int daysInMonth = calculateDaysInMonth(currentMonth, currentYear);
        cout << "     " << currentMonth << "/" << currentYear << endl;
        cout << "Su   Mo   Tu   We   Th   Fr   Sa" << endl;

        // Calculate the day of the week for the 1st day of the month
        struct tm firstDayOfMonth = {0, 0, 0, 1, currentMonth - 1, currentYear - 1900};
        mktime(&firstDayOfMonth);
        int dayOfWeek = firstDayOfMonth.tm_wday;

        // Print dates and events
        int dayCounter = 1;
        for (int i = 0; i < dayOfWeek; ++i) {
            cout << "   ";
            dayCounter++;
        }

        while (dayCounter <= daysInMonth) {
            cout << (dayCounter < 10 ? " " : "") << dayCounter << " ";

            // Check if there are events on this day
            bool hasEvent = false;
            for (size_t i = 0; i < events.size(); ++i) {
                int eventDay = stoi(events[i].date.substr(events[i].date.find_first_of('/') + 1, 2));
                if (eventDay == dayCounter) {
                    cout << "* ";  // Display a '*' to indicate an event on this day
                    hasEvent = true;
                    break;
                }
            }

            if (!hasEvent) {
                cout << "  ";
            }

            if ((dayOfWeek + dayCounter) % 7 == 0 || dayCounter == daysInMonth) {
                cout << endl;
            }

            dayCounter++;
        }
    }

    void addEvent(const string& eventName, int day, const string& eventTime) {
        if (day < 1 || day > 31) {
            cout << "Invalid day. Try again.\n";
            return;
        }

        string eventDate = to_string(currentMonth) + "/" + to_string(day) + "/" + to_string(currentYear);
        events.push_back(Event(eventName, eventDate, eventTime));
    }

    void deleteEvent(int eventNum) {
        if (eventNum < 1 || eventNum > static_cast<int>(events.size())) {
            cout << "Invalid event number. Try again.\n";
        } else {
            events.erase(events.begin() + eventNum - 1);
            cout << "Event deleted.\n";
        }
    }

    void modifyEvent(int eventNum, const string& newName, const string& newDate, const string& newTime) {
        if (eventNum < 1 || eventNum > static_cast<int>(events.size())) {
            cout << "Invalid event number. Try again.\n";
        } else {
            Event& event = events[eventNum - 1];
            event.name = newName;
            event.date = newDate;
            event.time = newTime;
            cout << "Event modified.\n";
        }
    }

    void viewEvents() {
        cout << "Events:\n";
        for (size_t i = 0; i < events.size(); ++i) {
            cout << i + 1 << ". " << events[i].name << " on " << events[i].date << " at " << events[i].time << "\n";
        }
    }

private:
    int calculateDaysInMonth(int month, int year) {
        int daysInMonth;
        if (month == 2) {
            if ((year % 4 == 0 && year % 100 != 0) || (year % 400 == 0)) {
                daysInMonth = 29;
            } else {
                daysInMonth = 28;
            }
        } else if (month == 4 || month == 6 || month == 9 || month == 11) {
            daysInMonth = 30;
        } else {
            daysInMonth = 31;
        }
        return daysInMonth;
    }
};


int main() {
    Calendar calendar;

    while (true) {
        calendar.display();

        cout << "\nOptions:\n";
        cout << "1. Add Event\n";
        cout << "2. View Events\n";
        cout << "3. Delete Event\n";
        cout << "4. Modify Event\n";
        cout << "5. Exit\n";

        int choice;
        cin >> choice;

        if (choice == 1) {
                   string eventName, eventTime;
                   int day;

                   cout << "Select a day to add an event (1-31): ";
                   cin >> day;

                   cout << "Event Name: ";
                   cin >> eventName;

                   cout << "Time (HH:MM AM/PM): ";
                   cin >> eventTime;

                   calendar.addEvent(eventName, day, eventTime);
               }else if (choice == 2) {
            calendar.viewEvents();
        } else if (choice == 3) {
            int eventNum;
            cout << "Enter the event number to delete: ";
            cin >> eventNum;
            calendar.deleteEvent(eventNum);
        } else if (choice == 4) {
            int eventNum;
            string newName, newDate, newTime;

            cout << "Enter the event number to modify: ";
            cin >> eventNum;

            cout << "New Event Name: ";
            cin >> newName;

            cout << "New Event Date (MM/DD/YYYY): ";
            cin >> newDate;

            cout << "New Event Time (HH:MM AM/PM): ";
            cin >> newTime;

            calendar.modifyEvent(eventNum, newName, newDate, newTime);
        } else if (choice == 5) {
            break;
        } else {
            cout << "Invalid choice. Try again.\n";
        }
    }

    return 0;
}


