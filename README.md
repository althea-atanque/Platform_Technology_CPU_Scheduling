// MLQ

#include <iostream>
#include <vector>
#include <iomanip>

using namespace std;

struct Process
{
    string pid;
    int arrivalTime;
    int burstTime;
    int queueLevel;

    int completionTime;
    int turnaroundTime;
    int waitingTime;
};

int main()
{
    int n;

    cout << "Enter number of processes: ";
    cin >> n;

    vector<Process> queue1;
    vector<Process> queue2;

    Process p;

    for(int i = 0; i < n; i++)
    {
        cout << "\nProcess ID: ";
        cin >> p.pid;

        cout << "Arrival Time: ";
        cin >> p.arrivalTime;

        cout << "Burst Time: ";
        cin >> p.burstTime;

        cout << "Queue Level (1 or 2): ";
        cin >> p.queueLevel;

        if(p.queueLevel == 1)
            queue1.push_back(p);
        else
            queue2.push_back(p);
    }

    int currentTime = 0;

    cout << "\nGantt Chart:\n| ";

    // Execute Queue 1
    for(int i = 0; i < queue1.size(); i++)
    {
        if(currentTime < queue1[i].arrivalTime)
            currentTime = queue1[i].arrivalTime;

        currentTime += queue1[i].burstTime;

        queue1[i].completionTime = currentTime;
        queue1[i].turnaroundTime =
            queue1[i].completionTime -
            queue1[i].arrivalTime;

        queue1[i].waitingTime =
            queue1[i].turnaroundTime -
            queue1[i].burstTime;

        cout << queue1[i].pid << " | ";
    }

    // Execute Queue 2
    for(int i = 0; i < queue2.size(); i++)
    {
        if(currentTime < queue2[i].arrivalTime)
            currentTime = queue2[i].arrivalTime;

        currentTime += queue2[i].burstTime;

        queue2[i].completionTime = currentTime;
        queue2[i].turnaroundTime =
            queue2[i].completionTime -
            queue2[i].arrivalTime;

        queue2[i].waitingTime =
            queue2[i].turnaroundTime -
            queue2[i].burstTime;

        cout << queue2[i].pid << " | ";
    }

    cout << "\n\n";

    cout << left
         << setw(8) << "PID"
         << setw(8) << "Q"
         << setw(8) << "AT"
         << setw(8) << "BT"
         << setw(8) << "CT"
         << setw(8) << "TAT"
         << setw(8) << "WT"
         << endl;

    double totalWT = 0;
    double totalTAT = 0;

    for(int i = 0; i < queue1.size(); i++)
    {
        cout << setw(8) << queue1[i].pid
             << setw(8) << queue1[i].queueLevel
             << setw(8) << queue1[i].arrivalTime
             << setw(8) << queue1[i].burstTime
             << setw(8) << queue1[i].completionTime
             << setw(8) << queue1[i].turnaroundTime
             << setw(8) << queue1[i].waitingTime
             << endl;

        totalWT += queue1[i].waitingTime;
        totalTAT += queue1[i].turnaroundTime;
    }

    for(int i = 0; i < queue2.size(); i++)
    {
        cout << setw(8) << queue2[i].pid
             << setw(8) << queue2[i].queueLevel
             << setw(8) << queue2[i].arrivalTime
             << setw(8) << queue2[i].burstTime
             << setw(8) << queue2[i].completionTime
             << setw(8) << queue2[i].turnaroundTime
             << setw(8) << queue2[i].waitingTime
             << endl;

        totalWT += queue2[i].waitingTime;
        totalTAT += queue2[i].turnaroundTime;
    }

    cout << fixed << setprecision(2);

    cout << "\nAverage Waiting Time: "
         << totalWT / n << endl;

    cout << "Average Turnaround Time: "
         << totalTAT / n << endl;

    return 0;
}
