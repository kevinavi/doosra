# doosra
Build Minified Uber

1. Store the details users  in database.
2. Storing the role of the user in separate table.
3. Store the cab driver is active or not.
4. If required store the details of ride.

----------------------------------------------------

Users
-- Id int
-- Name nvarchar
-- Phone number nvarchar
-- Details nvarchar
-- UserName nvarchar
-- Email nvarchar
-- Password nvarchar

RolesInformation
-- Id int
-- Name nvarchar

Roles
-- UserId -> Foreign reference from Users
-- RoleId -> Foreign reference from RolesInformation

DriverAvailability
-- UserId -> Foreign reference from Users (Assumption is that when a record is added it is thought that it is driver)
-- Active bit

------------------------------------------------------

Email, phone number, username

------------------------------------------------------

C#

public class User() {
    public string UserName {get;set;}
    public string FirstName {get;set;}
    public string LastName {get;set;}
    public string PhoneNumber {get;set;}
    public Role Role {get;set;}
}

public enum Role {
    Driver = 1,
    Rider = 2
}

var rider = new User {
    UserName = "user1",
    FirstName = "Avinash",
    LastName = "Naik",
    PhoneNumber = "9999999999",
    Role = Role.Rider
}

var driver = new User {
    UserName = "user2",
    FirstName = "Avinash",
    LastName = "Naik",
    PhoneNumber = "9999999999",
    Role = Role.Driver
}

public bool RegisterDriver(User user) {
    if(!ValidateUser(user)) {
        return false;
    }
    
    // Adding a user
    dbSet.Add<User>(user);
    
    // Adding the availability of driver into a table
    dbSet.Add<DriverAvailability>(new DriverAvailability {
        UserId = user.Id,
        Active = true
    });
    
    
    dbContext.SaveChanges();
}

public bool RegisterRider(User user) {
    if(!ValidateUser(user)) {
        return false;
    }
    
    // Adding a user
    dbSet.Add<User>(user);
    dbContext.SaveChanges();
}

public class Coordinates {
    public int x {get;set;}
    public int y {get;set;}
}

public IList<User> GetAllDrivers() {
    return db.User inner join db.DriverAvailability where Active = 1;
}

public Tuple<int?, int> BookACab(int riderId, Coordinates c) {
    IList<User> drivers = db.GetAllDrivers();
    
    // Get details of driver co-ordinates
    GetDriverLocations(drivers);
    
    decimal minDistance = MAX_INT;
    int? driverId = null;
    // Assumption is that I have only 10 drivers or minimal drivers.
    for(int i = 0; i < drivers.Count; i++) {
        var distanceBetweenRiderAndDriver = GetDistance(c, drivers[i]);
        if (distanceBetweenRiderAndDriver < minDistance) {
            minDistance = distanceBetweenRiderAndDriver
            driverId = i;
        }
    }
    
    return (driverId, riderId);
}

public decimal GetDistance(Coordinates riderCoordinates, Coordinates driverCoordinates) {
    return Math.sqrt(Math.pow((driverCoordinates.x - riderCoordinates.x), 2) + Math.pow((driverCoordinates.y - riderCoordinates.y), 2))
}

public bool ChangeTheLocation(int driverId, Coordinates c) {
    dbSet.Update<DriverAvailability>(new DriverAvailability {
        driverId,
        true
    });
    db.DriverLocations(new DriverLocation { driverId = driverId, x = c.x, y = c.y});
    dbContext.SaveChanges();
    
    return true;
}

// Assumption if driver is in the ride => DriverAvailability Active = 0
public bool EndTrip(int driverId) {
    dbSet.Update<DriverAvailability>(new DriverAvailability {
        driverId,
        true
    });
    dbContext.SaveChanges();
    
    return true;
}


--------------------------------------------------------------------------------------------
// It is a form with user details and along a with sign up button

function SignUp(userDetails) {
    var statuscode = axios.post('/api/signup', userDetails)
                        .then(response => response.statuscode);
                        
    if (statuscode == 200) {
        "login is successfull"
    }
}

function SignIn(userDetails) {
    var statuscode = axios.post('/api/signin', userDetails)
                        .then(response => response.statuscode);
                        
    if (statuscode == 200) {
        "login is successfull"
    }
}

// If user is a rider then we show a button saying that Book A cab
// If user is a driver then we show a button saying that Accept request from a rider
// After accepting the request from a driver we pop it up to the rider by giving driver details
function GetACab(riderId, coordinates) {
    var statuscode = axios.get('/api/book/cab?riderId={riderId}&riderX={coordinates.x}&riderY={coordinates.y}')
                        .then(response => response.statuscode);
                        
    if (statuscode == 200) {
        "got your request accpeted"
    }
}


function GetAllRequest(riderId) {

}

function AcceptRequest(riderId) {
    var statuscode = axios.post('/api/accept', {riderId, driverId})
                        .then(response => response.statuscode);
                        
    if (statuscode == 200) {
        "got your request accpeted"
    }
}

// After accepting the request we say move drivers availability to busy or inactive

function ChangeDriversAvailibity(driverId) {
    var statuscode = axios.get('/api/availability?driverId={driverId}')
                        .then(response => response.statuscode);
                        
    if (statuscode == 200) {
        "modified"
    }
}

// Ending trip

function EndTrip(driverId, riderId) {
    var statuscode = axios.get('/api/endtrip?driverId={driverId}&riderId={riderId}')
                        .then(response => response.statuscode);
                        
    if (statuscode == 200) {
        "modified"
    }
}
