// ROUND ROBIN

#include <iostream>
#include <iomanip>
#include <vector>
#include <queue>
#include <string>
#include <sstream>

using namespace std;

struct Process {
    string pid;
    int at, bt, remaining;
    int ct, tat, wt, rt;
    bool started;
};

struct Gantt {
    string pid;
    int start, end;
};

int main() {

    int n, quantum;

    cout << "========================================\n";
    cout << "   ROUND ROBIN CPU SCHEDULING SIMULATOR\n";
    cout << "========================================\n";

    cout << "\nEnter number of processes: ";
    cin >> n;

    if (n <= 0) {
        cout << "Invalid number of processes!\n";
        return 0;
    }

    vector<Process> p(n);

    // PROCESS INPUT
    cout << "\n========== PROCESS DETAILS ==========\n";

    for (int i = 0; i < n; i++) {

        stringstream ss;
        ss << "P" << (i + 1);
        p[i].pid = ss.str();

        cout << "\nProcess ID: " << p[i].pid << endl;

        cout << "Arrival Time: ";
        cin >> p[i].at;

        cout << "Burst Time: ";
        cin >> p[i].bt;

        if (p[i].at < 0 || p[i].bt <= 0) {
            cout << "Invalid input! AT must be >= 0 "
                 << "and BT must be > 0.\n";
            return 0;
        }

        p[i].remaining = p[i].bt;
        p[i].ct = 0;
        p[i].tat = 0;
        p[i].wt = 0;
        p[i].rt = 0;
        p[i].started = false;
    }

    // TIME QUANTUM
    cout << "\nEnter Time Quantum: ";
    cin >> quantum;

    if (quantum <= 0) {
        cout << "Invalid Time Quantum!\n";
        return 0;
    }

    // SORT PROCESSES BY ARRIVAL TIME
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {

            if (p[j].at > p[j + 1].at) {
                Process temp = p[j];
                p[j] = p[j + 1];
                p[j + 1] = temp;
            }
        }
    }

    queue<int> ready;
    vector<Gantt> chart;

    int time = 0;
    int completed = 0;
    int next = 0;
    int idleTime = 0;

    // ROUND ROBIN SIMULATION
    cout << "\n========== SIMULATION START ==========\n";

    while (completed < n) {

        // ADD ARRIVED PROCESSES
        while (next < n && p[next].at <= time) {
            ready.push(next);
            next++;
        }

        // CPU IDLE TIME
        if (ready.empty()) {

            if (next < n) {

                int idleStart = time;

                time = p[next].at;

                idleTime += time - idleStart;

                Gantt g;
                g.pid = "IDLE";
                g.start = idleStart;
                g.end = time;

                chart.push_back(g);

                cout << "CPU IDLE from "
                     << idleStart << " to " << time << endl;
            }

            continue;
        }

        // GET NEXT PROCESS
        int i = ready.front();
        ready.pop();

        // RESPONSE TIME
        if (p[i].started == false) {
            p[i].rt = time - p[i].at;
            p[i].started = true;
        }

        int start = time;

        // EXECUTE PROCESS
        int execution;

        if (p[i].remaining > quantum) {
            execution = quantum;
        } else {
            execution = p[i].remaining;
        }

        time = time + execution;

        p[i].remaining = p[i].remaining - execution;

        // RECORD GANTT CHART
        Gantt g;
        g.pid = p[i].pid;
        g.start = start;
        g.end = time;

        chart.push_back(g);

        cout << p[i].pid << " executes from "
             << start << " to " << time << endl;

        // ADD NEWLY ARRIVED PROCESSES
        while (next < n && p[next].at <= time) {
            ready.push(next);
            next++;
        }

        // CHECK IF PROCESS IS COMPLETED
        if (p[i].remaining == 0) {

            p[i].ct = time;

            p[i].tat = p[i].ct - p[i].at;

            p[i].wt = p[i].tat - p[i].bt;

            completed++;

            cout << p[i].pid << " COMPLETED!\n";

        } else {

            // PREEMPTION:
            // RETURN PROCESS TO THE END OF READY QUEUE

            ready.push(i);
        }
    }

    // DISPLAY GANTT CHART
    cout << "\n========== GANTT CHART ==========\n\n";

    for (int i = 0; i < (int)chart.size(); i++) {
        cout << "| " << chart[i].pid << " ";
    }

    cout << "|\n";

    for (int i = 0; i < (int)chart.size(); i++) {
        cout << setw(4) << chart[i].start;
    }

    cout << setw(4) << chart[chart.size() - 1].end << endl;

    // SCHEDULING RESULTS
    double totalWT = 0;
    double totalTAT = 0;
    double totalRT = 0;

    cout << "\n========== SCHEDULING RESULTS ==========\n\n";

    cout << left
         << setw(10) << "Process"
         << setw(😎 << "AT"
         << setw(😎 << "BT"
         << setw(😎 << "CT"
         << setw(😎 << "TAT"
         << setw(😎 << "WT"
         << setw(😎 << "RT"
         << endl;

    cout << "---------------------------------------------------------\n";

    // DISPLAY RESULTS IN ORIGINAL PROCESS ORDER
    for (int id = 1; id <= n; id++) {

        for (int i = 0; i < n; i++) {

            if (p[i].pid == "P" + string(1, char('0' + id))) {

                cout << left
                     << setw(10) << p[i].pid
                     << setw(😎 << p[i].at
                     << setw(😎 << p[i].bt
                     << setw(😎 << p[i].ct
                     << setw(😎 << p[i].tat
                     << setw(😎 << p[i].wt
                     << setw(😎 << p[i].rt
                     << endl;

                totalWT += p[i].wt;
                totalTAT += p[i].tat;
                totalRT += p[i].rt;
            }
        }
    }

    // DISPLAY AVERAGES
    cout << fixed << setprecision(2);

    cout << "\n========== AVERAGE RESULTS ==========\n";

    cout << "Average Waiting Time: "
         << totalWT / n << endl;

    cout << "Average Turnaround Time: "
         << totalTAT / n << endl;

    cout << "Average Response Time: "
         << totalRT / n << endl;

    cout << "Total CPU Idle Time: "
         << idleTime << endl;

    cout << "\n========== SIMULATION END ==========\n";

    return 0;
}

// FCFS








// SJF



//SRTF

#include <iostream>
#include <vector>
#include <iomanip>
#include <climits>
#include <string>

using namespace std;

struct Process
{
    int pid;
    int at;
    int bt;
    int ct;
    int tat;
    int wt;
    int rt;
    int firstStart;
    int remBt;
};

struct GanttItem
{
    string name;
    int start;
    int end;
};

void printGantt(const vector<GanttItem>& g)
{
    cout << "\n\nSRTF Gantt Chart\n";

    for(int i = 0; i < g.size(); i++)
    {
        cout << "| " << g[i].name << " ";
    }
    cout << "|\n";

    cout << "0";

    for(int i = 0; i < g.size(); i++)
    {
        cout << setw(5) << g[i].end;
    }

    cout << endl;
}

void printResult(const vector<Process>& proc)
{
    cout << "\n\nSRTF Scheduling Results\n\n";

    cout << left
         << setw(8) << "PID"
         << setw(6) << "AT"
         << setw(6) << "BT"
         << setw(6) << "CT"
         << setw(6) << "TAT"
         << setw(6) << "WT"
         << setw(6) << "RT"
         << endl;

    double totalTat = 0;
    double totalWt = 0;

    for(int i = 0; i < proc.size(); i++)
    {
        cout << left
             << setw(8) << proc[i].pid
             << setw(6) << proc[i].at
             << setw(6) << proc[i].bt
             << setw(6) << proc[i].ct
             << setw(6) << proc[i].tat
             << setw(6) << proc[i].wt
             << setw(6) << proc[i].rt
             << endl;

        totalTat += proc[i].tat;
        totalWt += proc[i].wt;
    }

    cout << fixed << setprecision(2);

    cout << "\nAverage Turnaround Time: "
         << totalTat / proc.size() << endl;

    cout << "Average Waiting Time: "
         << totalWt / proc.size() << endl;
}

vector<GanttItem> srtf(vector<Process>& proc)
{
    vector<GanttItem> gantt;

    int n = proc.size();
    int time = 0;
    int completed = 0;

    for(int i = 0; i < n; i++)
    {
        proc[i].remBt = proc[i].bt;
        proc[i].firstStart = -1;
    }

    int currIdx = -1;
    int startTime = 0;

    while(completed < n)
    {
        int idx = -1;
        int minRem = INT_MAX;

        for(int i = 0; i < n; i++)
        {
            if(proc[i].at <= time &&
               proc[i].remBt > 0 &&
               proc[i].remBt < minRem)
            {
                minRem = proc[i].remBt;
                idx = i;
            }
        }

        if(idx == -1)
        {
            time++;
            continue;
        }

        if(currIdx != idx)
        {
            if(currIdx != -1)
            {
                GanttItem temp;

                temp.name = "P";
                temp.name += char(proc[currIdx].pid + '0');

                temp.start = startTime;
                temp.end = time;

                gantt.push_back(temp);
            }

            currIdx = idx;
            startTime = time;

            if(proc[idx].firstStart == -1)
            {
                proc[idx].firstStart = time;
            }
        }

        proc[idx].remBt--;
        time++;

        if(proc[idx].remBt == 0)
        {
            GanttItem temp;

            temp.name = "P";
            temp.name += char(proc[idx].pid + '0');

            temp.start = startTime;
            temp.end = time;

            gantt.push_back(temp);

            proc[idx].ct = time;
            proc[idx].tat = proc[idx].ct - proc[idx].at;
            proc[idx].wt = proc[idx].tat - proc[idx].bt;
            proc[idx].rt = proc[idx].firstStart - proc[idx].at;

            currIdx = -1;
            completed++;
        }
    }

    return gantt;
}

int main()
{
    int n;

    cout << "===== SRTF Simulator =====" << endl;
    cout << "Enter number of processes: ";
    cin >> n;

    vector<Process> proc(n);

    for(int i = 0; i < n; i++)
    {
        proc[i].pid = i + 1;

        cout << "\nProcess P" << proc[i].pid << endl;

        cout << "Arrival Time: ";
        cin >> proc[i].at;

        cout << "Burst Time: ";
        cin >> proc[i].bt;
    }

    vector<GanttItem> gantt = srtf(proc);

    printGantt(gantt);
    printResult(proc);

    system("pause");
    return 0;
}



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
