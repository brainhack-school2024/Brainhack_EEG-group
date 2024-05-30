%% Load JFC dissertation PST REST data into BIDS with command-line scripts
clear all; clc

eeglabpath='Y:\Programs\eeglab2020_0\';  
addpath(eeglabpath); eeglab;

rootpath='Y:\EEG_Data\CAVANAGH\PL Cort Depression\FOR UPLOAD\';  addpath(rootpath);
rawdatapath='Y:\EEG_Data\CAVANAGH\PL Cort Depression\EEG Data\Rest Ready\';   cd(rawdatapath);
savepath=[rootpath,'BIDS\'];  

% We usually keep track of meta data using .xls files
[NUM,TXT,RAW]=xlsread([rootpath,'Data_4_Import.xlsx']);

%% Set up BIDS structures

% Content for README file
% -----------------------
README = sprintf( [ 'Resting EEG data with 122 college-age participants. These are the same participants as the Openneuro prob selection task. '...
                    'Subjects have the same task IDs, so you could match them up if you like.   '... 
                    'Task included in DMDX programming language, with instructions for eyes open & eyes closed  '...
                    'Triggers included for instrucgted one minute spans for open or closed, e.g. : OCCOCO or COOCOC   '...                    
                    'Data collected circa 2008-2010 in John J.B. Allen lab at U Arizona.  '...
                    'Subjects scored reliably high or low in Beck Depression Inventory.  Some have been clinically interviewed.  See .xls sheet. '...
                    'For some subjects (maybe all?), HEOG and VEOG may be mis-labeled as the other.  '...
                    'Some files have had some channels interpolated already.  There are no raw data to revert to instead...  '...
                    'I have never even looked at the last rest run; no idea how it looks.  First rest run was high quality though.  '...
                    'The first 6 mins happened immedately after EEG hook-up.  The second 6 minutes came after task performance (about 1 hour later)  '...
                    '516 has no rest2.  544 was unused in all anlayses due to unstable BDI between mass assessment and lab assessment (1-4 months)   '...
                    '- James F Cavanagh 01/18/2021' ]);
CHANGES = []; % Keep as null for inclusion below.  Not in use now, but maybe later.

% channel location file
% ---------------------
chanlocs = [eeglabpath,'\plugins\dipfit\standard_BESA\standard-10-5-cap385.elp'];
                
% general information for dataset_description.json file
% -----------------------------------------------------
gInfo.Name = 'Rest';
gInfo.ReferencesAndLinks = {  'PMID: 31149639' };
gInfo.Authors = {'James F Cavanagh  jcavanagh@unm.edu'};

% Task information for xxxx-eeg.json file
% ---------------------------------------
tInfo.InstitutionName = 'University of Arizona';
tInfo.InstitutionalDepartmentName = 'Psychology';
tInfo.PowerLineFrequency = 60;
tInfo.ManufacturersModelName = 'Neuroscan Synamps2';
% % tInfo.HardwareFilters.filter = {'.05-100'};
% % tInfo.SoftwareFilters.filter  = 'None';
tInfo.EEGGround = 'AFz';
tInfo.EEGReference = 'Between Cz & CPz';
tInfo.EEGChannelCount = 64;
tInfo.HEOGChannelCount = 1;
tInfo.VEOGChannelCount = 1;
tInfo.EKGChannelCount = 1;

% List of stimuli to be copied to the stimuli folder
% --------------------------------------------------
stimuli = {'Manually put in the folder'};


% event column description for xxx-events.json file (only one such file)
% ----------------------------------------------------------------------
eInfo = {'onset'         'latency';
         'value'         'type' };  

eInfoDesc.onset.Description = 'Event onset';
eInfoDesc.onset.Units = 'seconds';  % Trigger occurs this number of seconds into the file
eInfoDesc.value.Description = 'Trigger Code';  % What was the trigger

% Triggers overlapped each other, to make it easier to take out
% non-overlapping 2 second epochs (11-16) or 1 second epochs that
% overlapped by 50% (1-6) like standard frontal asymmetry methods.  
% Trigger 17 started and stopped the whole 6 separate minutes.'
trialTypes = { '17' 'Start and Finish'
    '1' 'Eyes Closed: Every 500 ms';
    '2' 'Eyes Open: Every 500 ms';
    '3' 'Eyes Closed: Every 500 ms';
    '4' 'Eyes Open: Every 500 ms';
    '5' 'Eyes Closed: Every 500 ms';
    '6' 'Eyes Open: Every 500 ms';
    '11' 'Eyes Closed: Every 2000 ms';
    '12' 'Eyes Open: Every 2000 ms';
    '13' 'Eyes Closed: Every 2000 ms';
    '14' 'Eyes Open: Every 2000 ms';
    '15' 'Eyes Closed: Every 2000 ms';
    '16' 'Eyes Open: Every 2000 ms';
    };

%%

% participant information for participants.tsv file (will pull from meta data .xls vars within the loading loop)
% -------------------------------------------------
pInfo = { 'participant_id'  'Original_ID'  'sex'   'age'   'BDI'  'STAI'  'SCID'  'SCID_notes'   'HamD'};  

% participant column description for participants.json file
% ---------------------------------------------------------
pInfoDesc.participant_id.Description = 'unique participant identifier';
pInfoDesc.Original_ID.Description = 'participant identifier from recording';
pInfoDesc.sex.Description = 'sex of the participant';
    pInfoDesc.sex.Levels.one = 'female';
    pInfoDesc.sex.Levels.two = 'male';
pInfoDesc.age.Description = 'age of the participant';
pInfoDesc.BDI.Description = 'Beck Depression Inventory score.  Subjects were recruited to be either consistencly low or consistently high.  See Comp Psychiatry paper for more info.';
pInfoDesc.STAI.Description = 'Speilberger Trait Anxiety Inventory score';
pInfoDesc.SCID.Description = 'Only Hi BDI (no CTL) were invited for SCID.  Many declined, so SCID only available for some high-BDI subjects.';
pInfoDesc.SCID_notes.Description =  'SCID outcome';
pInfoDesc.HamD.Description =  'Only people SCIDed had the HamD';
      
Filz=dir([rawdatapath,'*rest_ready.cnt']);
for si=1:length(Filz)
    
    filename=Filz(si).name;
    subno=str2num(filename(1:3));

    if subno~=516
        data(si).file{1} = filename;
        data(si).file{2} = strcat(filename(1:3),'rest2_ready.cnt');
        data(si).session = [1,1];
        data(si).run = [1,2];
        data(si).notes = '~~';
    elseif subno==516
        data(si).file = filename;
        data(si).session = 1;
        data(si).run = 1;  
        data(si).notes = 'Subj had to leave early';
    end
    
     if subno==544
        data(si).notes = 'Subj scored high on BDI in mass assessment but low when brought into lab.  Run by accident.  Not used in any anlayses ';         
     end
     
    numidx=find(NUM(:,1)==subno);
    pInfo{si+1,2}=subno;  
    pInfo{si+1,3}=NUM(numidx,5);  
    pInfo{si+1,4}=NUM(numidx,6);  
    pInfo{si+1,5}=NUM(numidx,7);
    pInfo{si+1,6}=NUM(numidx,11);
    % SCID info
    if      NUM(numidx,2)==99
        SCIDcode='No Interview';
    elseif  NUM(numidx,2)==1
        SCIDcode='Current MDD';
    elseif  NUM(numidx,2)==2
        SCIDcode='Past MDD';    
    elseif  NUM(numidx,2)==50
        SCIDcode='Do not meet criterion for current or past MDD'; 
    end
    pInfo{si+1,7}=SCIDcode;
    pInfo{si+1,8}=TXT{numidx+1,3};
    pInfo{si+1,9}=NUM(numidx,4);

end


%% call to the export function
% ---------------------------
clc;
targetFolder =  savepath;   % Defined above
bids_export(data, ...
    'targetdir', targetFolder, ...
    'taskName', gInfo.Name,...
    'trialtype', trialTypes, ...
    'gInfo', gInfo, ...
    'pInfo', pInfo, ...
    'pInfoDesc', pInfoDesc, ...
    'eInfo', eInfo, ...
    'eInfoDesc', eInfoDesc, ...
    'README', README, ...
    'CHANGES', CHANGES, ...
    'chanlookup', chanlocs, ...
    'tInfo', tInfo, ...
    'copydata', 0);    

  % Changes to bids_export.m
  % JFC edited lines 927-933:  EEGcoord system probs
  % JFC edited line 631:  EEG = pop_load-(fileIn, 'dataformat', 'int32', 'keystroke', 'on');  
