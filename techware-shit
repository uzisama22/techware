--[[

	techware outfit chnger gui djsfhdsfjdsfsdf
	simple gui made for making fake reports for da hood, enjoy lol
	this was made by misael on 10/13/2021
	also this was meant to be used on other users, not on yourself
	so if you're going to try and change ur avatar it's going to look weird or just kill you

	spanish note: gui hecho simplemente para falsear reportes de da hood, disfruten lol. fue hecho por misael el 13/10/2021
	spanish note2: ESTO NO FUNCIONA BIEN EN TU PROPIO PERSONAJE !!! ESTO FUE HECHO PARA USARLO EN OTROS JUGADORES !!! NO SE QUEJEN SI SU PERSONAJE SE MUERE O SE VE RARO SU AVATAR AL INTENTAR CAMBIAR SU OUTFIT !!!

	change logs:
	3/24/2022 release:
		wohoo made this shit public and open source!
		for the new folks here this is a script that changes another player outfit
		and also creates a fake player instance in the players service so it shows on the escape menu with their 
		properly inspect player button working
		this can be detectable by games so i'm not responsable if a game with a "decent" anti-cheat bans you for using this
		anyways, i'm releasing this open sourced since the code is shit and many things are pasted from the devforum 
		i haven't tested this shit with layered clothing so idk if it's going to work with those
		also there may be some bugs but i'm lazy to fix those, probably some avatars are going to be fucked up but eh who cares lol
		have fun!

	2/22/2022 update:
		made the inspect avatar button functional! (can be detected since i create a fake player in the player service)
		also fixed the fucking menu not updating the fake label when a new avatar has been changed
	
	2/20/2022 update: 
		woah i'm too late but i finally updated this shit an added name and userId spoofing (only using hookmetamethod so sorry if this doesn't work in ur exploit)
		and edits the coregui playershit so it looks more real (only escape menu), i hope soon i'll release this project open sourced 
		also i had to remove height and width since roblox doesn't change the scaling locally anymore.
		TODO: change the fucking player list from the right top
  
]]

-- old boronide shit
if not BC_WATERMARK then BC_WATERMARK=function()end end -- lazy ass
BC_WATERMARK("\n\n\t- TECHWARE OUTFIT CHANGER GUI CODIGO PRINCIPAL HECHO POR MISAEL!!!!!\t\n\n")

-- why?
if (not getgenv().show) then
	getgenv().show = "t"
end

-- // loading the gui
-- pasted shit from dex's code
local getobjects = function(a) -- Faster than game:GetObjects(a)
	local Objects = {}
	if a then
		local b = game:GetService("InsertService"):LoadLocalAsset(a)
		if b then 
			table.insert(Objects, b) 
		end
	end
	return Objects
end
local function Load(Obj, Url)
	local function GiveOwnGlobals(Func, Script)
		local Fenv, RealFenv, FenvMt = {}, {script = Script}, {}
		FenvMt.__index = function(a,b)
			return RealFenv[b] == nil and getgenv()[b] or RealFenv[b]
		end
		FenvMt.__newindex = function(a, b, c)
			if RealFenv[b] == nil then 
				getgenv()[b] = c 
			else 
				RealFenv[b] = c 
			end
		end
		setmetatable(Fenv, FenvMt)
		pcall(setfenv, Func, Fenv)
		return Func
	end

	local function LoadScripts(_, Script)
		if Script:IsA("LocalScript") and Script.Name ~= "Animate" and Script.Disabled == false then
			spawn(function()
				GiveOwnGlobals(loadstring(Script.Source,"="..Script:GetFullName()), Script)()
			end)
		end
	end
	for _, NewObj in next, Obj:GetDescendants() do 
		LoadScripts(nil, NewObj)
	end 
end

-- // ui shit
local ui = getobjects("rbxassetid://8888445574")[1]; -- old ui: 7730053114
pcall(function() -- =)
	syn.protect_gui(ui)
end)
ui.Name = math.random(349682,493854763) -- sfsdfsdfsdfdsfgh
ui.Parent = game:GetService("CoreGui")
Load(ui)

-- // variables and shit
local changedPlrs = {} -- for saving shit like original name, user id, spoofed name and shit yk the deal
local coreGui = game:GetService("CoreGui")
local players = game:GetService("Players")
local guiService = game:GetService("GuiService")
local httpService = game:GetService("HttpService")
local DefaultNPC = getobjects("rbxassetid://6509611495")[1] -- default R15 blocky package
local SFSGRGSJDFSFRGHJDASDHE_HEAD = getobjects("rbxassetid://7755893655")[1] -- "SDJSDFSGHRHEUDSF A FUCKING HEAD THAT ISN'T A MESHPART BUT A PART BECAUSE WHEN I TRY TO DO FUCKING STUFF THE ROBLOX ENGINE WHEN A PART NAMED "HEAD" IS ADDED TO A MODEL WITH A HUMANOID AUTOMATICALLY IS CONVERTED INTO A MESH PART?!?!?!?! WHAT THE FUCK ?!?!??!?!?!?!? IDK IF I'M ON DRUGS OR SOME SHIT BUT THAT IS WEIRD AND IDK IF SOMEONE ELSE HAS EXPERIENCED THAT BEFORE AND ALSO WHEN TRYING TO APPLY A FUCKING CUSTOM HEAD THE SIZE IS SO FUCKING SMALL AND WHEN I TRY TO USE SETSCRIPTABLE OR SETHIDDENPROPERTY FOR MeshSize IT DOESNT FUCKING WORKS, AND ALSO CHANGING THE HEAD SIZE DOESNT WORK TOO SO MY FUCKING SOLUTION IS TO MAKE THE ORIGINAL HEAD INVISIBLE AND WELD A FAKE HEAD INSIDE THAT IS A PART (NOT A MESHPART) AND ADD THE MESH FOR THE CUSTOM HEAD AJSDKJSFOEWIHFOHFSJDAD I FUCKING HATE R15" - average R6 enjoyer

-- some weld funcs for accessories (since addaccessory doesn't work in the client) pasted from: https://devforum.roblox.com/t/humanoidaddaccessory-does-not-work-with-fe-from-a-localscript/33657/4
function weldAttachments(attach1, attach2)
	local weld = Instance.new("Weld")
	weld.Part0 = attach1.Parent
	weld.Part1 = attach2.Parent
	weld.C0 = attach1.CFrame
	weld.C1 = attach2.CFrame
	weld.Parent = attach1.Parent
	return weld
end

local function buildWeld(weldName, parent, part0, part1, c0, c1)
	local weld = Instance.new("Weld")
	weld.Name = weldName
	weld.Part0 = part0
	weld.Part1 = part1
	weld.C0 = c0
	weld.C1 = c1
	weld.Parent = parent
	return weld
end

local function findFirstMatchingAttachment(model, name)
	for _, child in pairs(model:GetChildren()) do
		if child:IsA("Attachment") and child.Name == name then
			return child
		elseif not child:IsA("Accoutrement") and not child:IsA("Tool") then -- Don't look in hats or tools in the character
			local foundAttachment = findFirstMatchingAttachment(child, name)
			if foundAttachment then
				return foundAttachment
			end
		end
	end
end

function addAccoutrement(character, accoutrement)  
	accoutrement.Parent = character
	local handle = accoutrement:FindFirstChild("Handle")
	if handle then
		local accoutrementAttachment = handle:FindFirstChildOfClass("Attachment")
		if accoutrementAttachment then
			local characterAttachment = findFirstMatchingAttachment(character, accoutrementAttachment.Name)
			if characterAttachment then
				weldAttachments(characterAttachment, accoutrementAttachment)
			end
		else
			local head = character:FindFirstChild("Head")
			if head then
				local attachmentCFrame = CFrame.new(0, 0.5, 0)
				local hatCFrame = accoutrement.AttachmentPoint
				buildWeld("HeadWeld", head, head, handle, attachmentCFrame, hatCFrame)
			end
		end
	end
end

-- // changing outfit shit
local rgui = coreGui:WaitForChild("RobloxGui")
local ss = rgui:WaitForChild("SettingsShield"):WaitForChild("SettingsShield")
local innerFrame = ss:WaitForChild("MenuContainer"):WaitForChild("PageViewClipper"):WaitForChild("PageView"):WaitForChild("PageViewInnerFrame")
guiService.MenuOpened:Connect(function()
	local plrsFrame = innerFrame:WaitForChild("Players")
	plrsFrame:WaitForChild("RowListLayout").SortOrder = Enum.SortOrder.Name
	task.wait(0.04) -- :P
	for _, obj in next, plrsFrame:GetChildren() do 
		if (obj:IsA("ImageLabel") and obj.Name:find("PlayerLabel")) then
			local plrName = obj.Name:sub(12)
			local realPlr = players:FindFirstChild(plrName)
			if (realPlr and changedPlrs[realPlr] and changedPlrs[realPlr]["FakePlayer"] ~= nil) then
				-- now we just hide the original player label (3/24 edit: why the fuck i'm doing this? idk)
				obj.Visible = false
				changedPlrs[realPlr].RealLabel = obj
				--[[
					legacy fake label shit, now we add a fake player in the player service since i don't have an way 
					to properly show an inspect player menu

					local fakeLabel = obj:Clone()
					fakeLabel.Name = "PlayerLabel"..info.FakeName
					fakeLabel.Icon.Image = "rbxthumb://type=Avatar&id="..info.FakeUserId.."&w=100&h=100"
					fakeLabel.DisplayNameLabel.Text = info.FakeDisplayName
					fakeLabel.NameLabel.Text = "@"..info.FakeName
					fakeLabel.Parent = plrsFrame
					fakeLabel.Visible = true
					changedPlrs[realPlr].FakeLabel = fakeLabel
					changedPlrs[realPlr].RealLabel = obj
					task.spawn(function()
						pcall(function()
							fakeLabel:WaitForChild("RightSideButtons"):WaitForChild("Inspect").MouseButton1Down:Connect(function() 
								guiService:InspectPlayerFromUserIdWithCtx(info.FakeUserId, "escapeMenu")
							end)
						end)
					end)
				]]
			end
		end
	end
end)

-- only supports synx, krnl and other exploits that has hookmetamethod for now, sorry!
pcall(function()
	oldIndex = hookmetamethod(game, "__index", function(self, ind)
		-- we are going to spoof this shit only to localscripts
		if (changedPlrs[self] and changedPlrs[self]["Fake"..ind] and not checkcaller()) then -- probably detectable but eh, i'm not going to update this script anyways lmao
			return changedPlrs[self]["Fake"..ind]
		end
		return oldIndex(self, ind)
	end)
end)

function removeData(p)
	if (changedPlrs[p]) then
		if (changedPlrs[p]["FakePlayer"]) then
			pcall(function()
				changedPlrs[p]["FakePlayer"]:Destroy()
			end)
			changedPlrs[p]["FakePlayer"] = nil 
		end
		if (changedPlrs[p]["RealLabel"]) then
			pcall(function()
				changedPlrs[p]["RealLabel"].Visible = true
			end)
		end
		changedPlrs[p] = nil
	end
end

function setOutfit(userId,plrChar)
	local success,assetsModel = pcall(players.GetCharacterAppearanceAsync,players,userId)
	local information = game:HttpGet("https://users.roblox.com/v1/users/"..userId); -- for the displayName shit :]
	information = httpService:JSONDecode(information);
	if success and assetsModel then
		local rigType = plrChar.Humanoid.RigType
		-- time to "clean" the character !!!
		for i,v in pairs(plrChar:GetChildren()) do
			if v:IsA("Accessory") or v:IsA("Shirt") or v:IsA("Pants") or v:IsA("ShirtGraphic") then 
				v:Destroy()
			end
		end
		if plrChar:FindFirstChild("Head") and plrChar.Head:FindFirstChild("face") then -- just in case if for some odd reason the character doesn't have a head or face lol
			plrChar.Head.face:Destroy()
		end
		-- we remove the package that the player had before
		if rigType == Enum.HumanoidRigType.R15 then -- r15
			for _,part in next, DefaultNPC:Clone():GetChildren() do
				pcall(function()
					plrChar.Humanoid:ReplaceBodyPartR15(Enum.BodyPartR15[part.Name],part)
				end)				
			end
		else -- r6
			for _,obj in next, plrChar:GetChildren() do
				if obj:IsA("CharacterMesh") then
					obj:Destroy()
				end
			end
		end
		wait(0.1)
		-- if the model doesn't have a "face" decal then we assume that the player 
		-- has the default face in their outfit
		if not assetsModel:FindFirstChild("face") then
			pcall(function()
				local defaultFace = Instance.new("Decal")
				defaultFace.Texture = "rbxasset://textures/face.png"
				defaultFace.Face = "Front"
				defaultFace.Name = "face"
				defaultFace.Parent = plrChar.Head
			end)
		end
		-- displayName shit
		if plrChar:FindFirstChild("Humanoid") then
			local displayname = information.displayName;
			if not _G.starEnabled then
				plrChar.Humanoid.DisplayName = displayname
			else
				plrChar.Humanoid.DisplayName = "[⭐] "..displayname
			end
		end
		wait(0.75)
		-- fucking head mesh support
		-- you may ask "why are you making a new head and welding it to the original head"
		-- well, just changing the "MeshId" property of a meshpart head doesn't work properly
		-- makes it so fucking small for some head meshes and trying to change the size doesnt work
		-- so, we hide the original head and create a fake head that works !!!!
		-- probably there's a fix to that but 4 now i can't think of one, sorry !!!
		if assetsModel:FindFirstChild("Mesh") then
			if rigType == Enum.HumanoidRigType.R15 then -- r15
				plrChar.Head.Transparency = 1 
				local newHeadMesh = assetsModel.Mesh;
				local model = Instance.new("Model");
				model.Name = "FakeHead";
				model.Parent = plrChar.Head;
				local humClone = plrChar.Humanoid:Clone();
				humClone.Parent = model;
				local fakeHead = SFSGRGSJDFSFRGHJDASDHE_HEAD.Head:Clone();
				fakeHead.Parent = model;
				local weld = Instance.new("Weld")
				weld.Part0 = plrChar.Head;
				weld.Part1 = fakeHead;
				weld.Parent = fakeHead;
				fakeHead.Mesh:Destroy();
				newHeadMesh.Parent = fakeHead;
				pcall(function()
					-- let's say you are playing a game where you can damage others
					-- to make this look real, we change the health of the humanoid clone when the original one is damaged or something
					plrChar.Humanoid.HealthChanged:Connect(function(newhealth)
						humClone.Health = newhealth
					end)
				end)
			else -- r6
				local newHeadMesh = assetsModel.Mesh
				local head = plrChar.Head
				if head:FindFirstChildOfClass("SpecialMesh") then 
					head:FindFirstChildOfClass("SpecialMesh"):Destroy()
				end
				newHeadMesh.Parent = head
			end
		else
			if rigType == Enum.HumanoidRigType.R6 then -- we only do this shit in R6 since we don't need to do this for R15
				local head = plrChar.Head
				if head:FindFirstChildOfClass("SpecialMesh") then 
					head:FindFirstChildOfClass("SpecialMesh"):Destroy()
				end
				local newmesh = Instance.new("SpecialMesh")
				newmesh.MeshType = Enum.MeshType.Head 				
				newmesh.Offset = Vector3.new(0,0,0)
				newmesh.Scale = Vector3.new(1.25,1.25,1.25)
				newmesh.VertexColor = Vector3.new(1,1,1)
				newmesh.Parent = head
			end
		end
		-- we apply body shit
		for _,v in next, assetsModel:GetChildren() do
			-- bodyParts R15
			if v.Name == "R15Fixed" then
				if rigType == Enum.HumanoidRigType.R15 then
					for _,bb in next, v:GetChildren() do
						if plrChar:FindFirstChild("Humanoid") then
							plrChar.Humanoid:ReplaceBodyPartR15(Enum.BodyPartR15[bb.Name],bb)
						end
					end
				end
			end
			-- bodyParts R6
			if v.Name == "R6" then
				if rigType == Enum.HumanoidRigType.R6 then
					for _,bb in next, v:GetChildren() do
						if bb:IsA("CharacterMesh") then
							bb.Parent = plrChar
						end
					end
				end
			end
			-- bodyColors
			if v.Name == "Body Colors" then
				if plrChar:FindFirstChild("Body Colors") then
					plrChar["Body Colors"]:Destroy()
				end
				pcall(function()
					if rigType == Enum.HumanoidRigType.R15 and plrChar.Head:FindFirstChild("FakeHead") then
						v:Clone().Parent = plrChar.Head.FakeHead;
					end
				end)
				v.Parent = plrChar
			end
		end
		-- now we apply the rest of the clothing
		for _,v in next, assetsModel:GetChildren() do
			-- face
			if v.Name == "face" then
				pcall(function()
					v.Parent = plrChar.Head
				end)
			end
			-- accessories
			if v:IsA("Accessory") then
				if plrChar:FindFirstChild("Head") then
					addAccoutrement(plrChar,v)
				end
			end
			-- shirt, pants and t-shirts (shirtGraphics)
			if v:IsA("Pants") or v:IsA("Shirt") or v:IsA("ShirtGraphic") then
				v.Parent = plrChar
			end
		end
		pcall(function()
			if rigType == Enum.HumanoidRigType.R15 and plrChar.Head:FindFirstChild("FakeHead") then
				plrChar.Head:FindFirstChildOfClass("Decal"):Clone().Parent = plrChar.Head.FakeHead.Head
				plrChar.Head:FindFirstChildOfClass("Decal").Transparency = 1
			end
		end)
		-- sfdssnsg
		local realPlr = players:GetPlayerFromCharacter(plrChar)
		removeData(realPlr)
		changedPlrs[realPlr] = {
			["FakeName"] = information.name,
			["FakeDisplayName"] = information.displayName,
			["FakeUserId"] = information.id,
			["FakeCharacterAppearanceId"] = information.id
		}
		local fakePlr = Instance.new("Player") -- fake player, more easy than changing coregui shit lol
		local info = changedPlrs[realPlr];
		fakePlr.Name = info.FakeName
		fakePlr.DisplayName = info.FakeDisplayName
		fakePlr.UserId = info.FakeUserId
		changedPlrs[realPlr].FakePlayer = fakePlr -- probably can be detected by localScripts by doing: if (localPlayer.Player) then print'hax' end; probably i'll fix the metamethod hook another day but not for now!!!
		fakePlr.Parent = players;
	end
end

for _,v in next, players:GetPlayers() do 
	v.CharacterAdded:Connect(function()
		removeData(v)
	end)
end

players.PlayerAdded:Connect(function(v)
	v.CharacterAdded:Connect(function()
		removeData(v)
	end)
end)

players.PlayerRemoving:Connect(function(v)
	removeData(v)
end)

ui.MainFrame.Confirm.MouseButton1Down:Connect(function()
	if _G.selectedPlr == nil then 
		-- warn("not selected player")
		return;
	end 
	local text = ui.MainFrame.Outfit.Text
	if tonumber(text) then 
		local isvalid = pcall(players.GetNameFromUserIdAsync, players, text)
		if not isvalid then 
			-- warn("not valid userid")
			return;
		end 
		setOutfit(text, _G.selectedPlr.Character)
	else 
		local isvalid, id = pcall(players.GetUserIdFromNameAsync, players, text)
		if not isvalid then 
			-- warn("not valid username")
			return;
		end 
		setOutfit(id, _G.selectedPlr.Character)
	end 
end)
