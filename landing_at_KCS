clearscreen.
sas off.
lights off.
set addons:tr:descentangles to list(90,90,90,90).
list engines in en.

//set tgt to latlng(-0.097229,-74.557686). //launchpad
set tgt to latlng(-0.096829,-74.618215). //rooftop
//set tgt to latlng(-0.049659,-74.617535). //runway center
//set tgt to latlng(-0.075608,-74.640433). //ksc parking slot
//set tgt to latlng(-1.518035,-71.89934). //Island Airfield
set hd to tgt:heading.
//set bfh to 830+100. //V on roof


//KS R 650

set bfh to 38.2*ship:drymass-140. //KS 855
set bfa to 75/(constant:pi/180*kerbin:radius). 
print "Kerbalship".
print "bfh: " + round(bfh,1).


wait 2.


set landingangle to 90.
set v0 to 15.
set steeringmanager:rolltorquefactor to 0.35.

lock steering to (-1)*ship:velocity:orbit.
print "waiting for deorbit burn...".

until ship:geoposition:lng > tgt:lng - landingangle and ship:geoposition:lng < tgt:lng - landingangle + 0.025 {
clearscreen.
print "ship longitude: " + round(ship:geoposition:lng,2).
print "deorbit burn longitude: " + round(tgt:lng - landingangle,2).
wait 0.001.
}

set a to 1.

until ship:periapsis < -1000 {
 lock steering to (-1)*ship:velocity:orbit.
 lock throttle to a.

 clearscreen.
 print "deorbit burn".
 print "periapsis: " + round(ship:orbit:periapsis,0) + " m".
 wait 0.001.
 }

until abs(tgt:lng-addons:tr:impactpos:lng) < 1.75 {
 lock steering to (-1)*ship:velocity:orbit.
 lock throttle to a.

 clearscreen.
 print "deorbit burn".
 print "periapsis: " + round(ship:orbit:periapsis,0) + " m".
 print "finalizing trajectory...".
 wait 0.001.
 }

until (addons:tr:impactpos:lng-tgt:lng) < 0.02 {
set throttle to 0.075.
clearscreen.
print "adjusting trajectory".
wait 0.001.
}

set throttle to 0.

clearscreen.
print "deorbit burn completed".
print "periapsis: " + round(ship:orbit:periapsis,0) + "m".

wait 1.

set step to 0.
set drainstep to 0.

until alt:radar < bfh {
 set throttle to 0.
 set distancediff to arccos(cos(abs(tgt:lat-addons:tr:impactpos:lat))*cos(abs(tgt:lng-addons:tr:impactpos:lng)))/180*constant:pi*kerbin:radius.
 set incdiff to (tgt:lat-addons:tr:impactpos:lat)*constant:pi/180*kerbin:radius.
 set distance to (tgt:lng-bfa- ship:geoposition:lng)/180*constant:pi*kerbin:radius.
 set a to ship:sensors:acc:mag/constant:g0.

if abs(incdiff) > 0.25 and ship:altitude < 69000 {
 AG4 off.
 rcs on.
 if incdiff < 0 {
 set ship:control:starboard to min(abs(incdiff)/15,0.5).
} else if incdiff > 0 {
 set ship:control:starboard to max(-abs(incdiff)/15,-0.5).
}
} else {
set ship:control:neutralize to true.
}

set dpmax to min((10.0/70*(340-ship:velocity:surface:mag)),10.0).
set dpmin to -1*dpmax.

set dp to max(min(-10/50*(tgt:lng-bfa-addons:tr:impactpos:lng)/180*constant:pi*kerbin:radius,dpmax),dpmin).

if ship:velocity:surface:mag > 340 {set dp to 0.}
else if alt:radar < 1000 {set dp to 0.}
else if ship:groundspeed < 15 and distance < 100 and alt:radar > 1000 {
set dp to max(min(3/1000*(4000-ship:altitude),1.5),1)* dp.
}

set p to 90-arctan(-verticalspeed/ship:groundspeed)+dp.

set steering to heading(90,p).

 if ship:altitude < 70000 {
 rcs on.
 AG4 off.
 }

//steeringmanager:pitcherror

if (en[0]:gimbal:pitchangle < -0.03 and ship:altitude < 65000) {
set transfer("oxidizer", ship:partsdubbed("Tank2"), ship:partsdubbed("Tank1"), min(3*abs(en[0]:gimbal:pitchangle),3)):active to true.
set transfer("liquidfuel", ship:partsdubbed("Tank2"), ship:partsdubbed("Tank1"), min(3*abs(en[0]:gimbal:pitchangle),3)):active to true.
}
else if en[0]:gimbal:pitchangle > 0.03 and ship:altitude < 65000{
set transfer("oxidizer", ship:partsdubbed("Tank1"), ship:partsdubbed("Tank2"), min(3*abs(en[0]:gimbal:pitchangle),3)):active to true.
set transfer("liquidfuel", ship:partsdubbed("Tank1"), ship:partsdubbed("Tank2"), min(3*abs(en[0]:gimbal:pitchangle),3)):active to true.
}

if (ship:altitude < 10000 and drainstep = 0) {
toggle AG8.
set drainstep to 1.
print "draining".
}
else if (ship:deltav:current < 500 and drainstep = 1){
toggle AG9.
set drainstep to 2.}
else if ship:mass < 24.2 {
toggle AG9.
set drainstep to 2.}

set addons:tr:descentangles to list(90,90,90+0.05*dp,90+0.05*dp).

clearscreen.
 print "radar altitude: " + round(alt:radar,0) + " m".
 print "bellyflop altitude: " + round(bfh,0) + " m".
print "------------------------------".
 print "surface velocity: " + round(ship:velocity:surface:mag,1) + " m/s".
 print "groundspeed: " + round(ship:groundspeed,1) + " m/s".
print "------------------------------".
 print "pitch input: " + round(en[0]:gimbal:pitchangle,2).
 print "tgt pitch: " + round(p,2) + " deg".
 print "glide pitch: " + round(dp,2) + " deg".
print "------------------------------".
 print "distance tgt: " + round(distancediff,1) + " m".
 print "distance ship: " + round(distance,1) + " m".
print "------------------------------".
 print "tgt heading: " + round(tgt:heading,1).

if ship:groundspeed < 15 and distance < 100 and alt:radar > 1000 {
print "final approach... preparing belly flop".
} 
else if ship:velocity:surface:mag > 340 {
print "reentry mode".
}
else {
print "glide mode".
}

 wait 0.05.
}

set shipUp to (-1)*ship:facing:forevector.
set transferall("oxidizer", ship:partsdubbed("Tank1"), ship:partsdubbed("Tank2")):active to true.
//set transferall("liquidfuel", ship:partsdubbed("Tank1"), ship:partsdubbed("Tank2")):active to true.

print "----------".
print "landing sequence initiated".
print "bellyflop has begun".
rcs off.
set normVec to vcrs(ship:velocity:surface,body:position).
set radVec to vcrs(ship:velocity:surface,normVec).
set retroVec to (-1)*ship:velocity:surface.
set pitch to -90.

until pitch > 50 {
set throttle to 1.
lock steering to angleaxis(-70,ship:facing:starvector)*ship:facing.
set pitch to 90 - vectorangle(ship:up:forevector, ship:facing:forevector).
wait 0.001.
}

print "slowing down".

until verticalspeed > -15 {
 lock steering to lookdirup((-1)*ship:velocity:surface, shipUp).
 set throttle to 1.
 wait 0.001.
}

until verticalspeed > -2 {
 if verticalspeed > -7
 {
 set steering to lookdirup((-1)*ship:body:position, shipUp). 
 }
 else
 {
 set steering to lookdirup((-1)*ship:velocity:surface, shipUp).
 }
 set a to ((verticalspeed^2/(2*(alt:radar-20)) + constant:g*body:mass/body:radius^2)*ship:mass)/ship:availablethrust.
 set throttle to a.
 if alt:radar < 100
 {
 gear on.
 }
 wait 0.001.
}

print "expecting touchdown...".

until verticalspeed > -0.5 {
 set steering to lookdirup(ship:up:forevector, shipUp).
 set a to (ship:mass*constant:g * body:mass / body:radius^2)/ship:AVAILABLETHRUST.
 set throttle to a.
 wait 0.001.
}

print "ship landed".
set distance to arccos(cos(abs(tgt:lat-ship:geoposition:lat))*cos(abs(tgt:lng-ship:geoposition:lng)))/180*constant:pi*kerbin:radius.
print "distance: " + round(distance,2) + " m".
set throttle to 0.
set pitch to 60.
until pitch > 88 {
set pitch to 90 - vectorangle(ship:up:forevector, ship:facing:forevector).
if pitch < 87 {
set steering to up.
rcs off.
} else { 
unlock steering.
unlock throttle.
rcs off.
wait 1.
}}
