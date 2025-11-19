// ================== DOM Elements ==================
const speedValueEl = document.getElementById('speed-value');
const speedUnitEl = document.getElementById('speed-unit');
const rpmContainerEl = document.getElementById('rpm-bar-container');
const fuelBarEl = document.getElementById('fuel-bar');
const healthBarEl = document.getElementById('health-bar');
const gearValueEl = document.getElementById('gear-value');
const leftIndicatorEl = document.getElementById('left-indicator');
const rightIndicatorEl = document.getElementById('right-indicator');
const headlightsIconEl = document.getElementById('headlights-icon');
const seatbeltIconEl = document.getElementById('seatbelt-icon');
const engineIconEl = document.getElementById('engine-icon');
const alarmIndicatorEl = document.getElementById('alarm-indicator');
const alarmStatusTextEl = document.getElementById('alarm-status-text');

// ================== Audio Elements ==================
const engineStartSound = document.getElementById('engine-start-sound');
const engineStopSound = document.getElementById('engine-stop-sound');
const indicatorSound = document.getElementById('indicator-sound');
const seatbeltWarningSound = document.getElementById('seatbelt-warning-sound');
const alarmSound = document.getElementById('alarm-sound');

// ================== Config ==================
let currentSpeedMode = 0;
const SPEED_MODES = ['km/h', 'mph', 'knots'];
const CONVERSION_FACTORS = [3.6, 2.23694, 1.94384];
const RPM_SEGMENTS_COUNT = 20;
const RPM_DANGER_THRESHOLD = 0.85;

// ================== State ==================
let previousEngineState = false;
let indicatorSoundInterval = null;
let seatbeltWarningInterval = null;
let alarmActive = false;
let alarmStartTime = null;

// ================== Functions ==================

function setEngine(state) {
    if (state !== previousEngineState) {
        engineIconEl.classList.toggle('active', state);
        if (state) {
            engineStartSound.currentTime = 0;
            engineStartSound.play();
        } else {
            engineStopSound.currentTime = 0;
            engineStopSound.play();
        }
        previousEngineState = state;
    }
}

function setSpeed(speed) {
    const convertedSpeed = speed * CONVERSION_FACTORS[currentSpeedMode];
    speedValueEl.textContent = Math.floor(convertedSpeed);
}

function setRPM(rpm) {
    const activeSegments = Math.round(rpm * RPM_SEGMENTS_COUNT);
    const dangerSegments = Math.round(RPM_DANGER_THRESHOLD * RPM_SEGMENTS_COUNT);

    for (let i = 0; i < RPM_SEGMENTS_COUNT; i++) {
        const segment = rpmContainerEl.children[i];
        if (i < activeSegments) {
            segment.classList.add('active');
            segment.classList.toggle('danger', i >= dangerSegments);
        } else {
            segment.classList.remove('active', 'danger');
        }
    }
}

function setFuel(fuel) {
    const percentage = Math.max(0, Math.min(100, fuel * 100));
    fuelBarEl.style.width = `${percentage}%`;
}

function setHealth(health) {
    const percentage = Math.max(0, Math.min(100, health * 100));
    healthBarEl.style.width = `${percentage}%`;
}

function setGear(gear) {
    if (gear === 0) {
        gearValueEl.textContent = 'N';
    } else if (typeof gear === 'string') {
        gearValueEl.textContent = gear.toUpperCase();
    } else {
        gearValueEl.textContent = gear;
    }
}

function setHeadlights(state) {
    headlightsIconEl.classList.remove('low-beam', 'high-beam');
    switch (state) {
        case 1:
            headlightsIconEl.classList.add('low-beam');
            break;
        case 2:
            headlightsIconEl.classList.add('high-beam');
            break;
    }
}

function setLeftIndicator(state) {
    leftIndicatorEl.classList.toggle('blinking', state);
    handleIndicatorSound(state || rightIndicatorEl.classList.contains('blinking'));
}

function setRightIndicator(state) {
    rightIndicatorEl.classList.toggle('blinking', state);
    handleIndicatorSound(state || leftIndicatorEl.classList.contains('blinking'));
}

function handleIndicatorSound(active) {
    if (active && !indicatorSoundInterval) {
        indicatorSoundInterval = setInterval(() => {
            if (leftIndicatorEl.classList.contains('blinking') || rightIndicatorEl.classList.contains('blinking')) {
                indicatorSound.currentTime = 0;
                indicatorSound.play().catch(() => {});
            } else {
                clearInterval(indicatorSoundInterval);
                indicatorSoundInterval = null;
            }
        }, 600);
    } else if (!active) {
        if (indicatorSoundInterval) {
            clearInterval(indicatorSoundInterval);
            indicatorSoundInterval = null;
        }
        indicatorSound.pause();
        indicatorSound.currentTime = 0;
    }
}

function setSeatbelts(state) {
    seatbeltIconEl.classList.toggle('active', !state);
    if (!state && !seatbeltWarningInterval) {
        seatbeltWarningInterval = setInterval(() => {
            seatbeltWarningSound.currentTime = 0;
            seatbeltWarningSound.play().catch(() => {});
        }, 2000);
    } else if (state) {
        if (seatbeltWarningInterval) {
            clearInterval(seatbeltWarningInterval);
            seatbeltWarningInterval = null;
        }
        seatbeltWarningSound.pause();
        seatbeltWarningSound.currentTime = 0;
    }
}

function setSpeedMode(mode) {
    if (mode >= 0 && mode < SPEED_MODES.length) {
        currentSpeedMode = mode;
        speedUnitEl.textContent = SPEED_MODES[currentSpeedMode];
    }
}

// ================== Alarm System ==================
function startAlarm() {
    if (!alarmActive) {
        alarmActive = true;
        alarmStartTime = Date.now();
        alarmSound.currentTime = 0;
        alarmSound.play().catch(() => {});
        updateAlarmIndicator();
    }
}

function stopAlarm() {
    if (alarmActive) {
        alarmActive = false;
        alarmSound.pause();
        alarmSound.currentTime = 0;
        updateAlarmIndicator();
        alarmStartTime = null;
    }
}

function toggleAlarm() {
    if (alarmActive) stopAlarm();
    else startAlarm();
}

function updateAlarmIndicator() {
    if (!alarmIndicatorEl || !alarmStatusTextEl) return;
    if (alarmActive) {
        alarmIndicatorEl.classList.remove('inactive');
        alarmIndicatorEl.classList.add('active');
        alarmStatusTextEl.textContent = 'SAFE';
    } else {
        alarmIndicatorEl.classList.remove('active');
        alarmIndicatorEl.classList.add('inactive');
        alarmStatusTextEl.textContent = 'SAFE';
    }
}

document.addEventListener('keydown', (event) => {
    if (event.shiftKey && (event.key === 'A' || event.key === 'a')) {
        event.preventDefault();
        stopAlarm();
    }
});

// ================== Init ==================
function initializeDashboard() {
    rpmContainerEl.innerHTML = '';
    for (let i = 0; i < RPM_SEGMENTS_COUNT; i++) {
        const segment = document.createElement('div');
        segment.classList.add('rpm-segment');
        rpmContainerEl.appendChild(segment);
    }
    updateAlarmIndicator();
}

window.onload = initializeDashboard;
