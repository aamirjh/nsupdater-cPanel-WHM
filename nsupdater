#!/bin/bash

# -----------------------
# nsupdater v2 by Nimit
# -----------------------

# Color codes
RED=$(tput setaf 1)
GREEN=$(tput setaf 2)
YELLOW=$(tput setaf 3)
CYAN=$(tput setaf 6)
BOLD=$(tput bold)
RESET=$(tput sgr0)

# Paths
NAMED_DIR="/var/named"
TIMESTAMP=$(date +%Y%m%d_%H%M)
BACKUP_DIR="/root/nsupdater_backup_$TIMESTAMP"
LOG_FILE="/root/nsupdater.log"

# Initialize counters
total=0; updated=0; skipped=0; failed=0

# Flags
DRY_RUN=false
ROLLBACK=false

# Handle script arguments
if [[ "$1" == "--dry-run" ]]; then
    DRY_RUN=true
    echo -e "${YELLOW}[DRY RUN MODE]${RESET} No files will be changed.\n"
elif [[ "$1" == "--rollback" ]]; then
    ROLLBACK=true
    echo -e "${RED}[ROLLBACK MODE]${RESET} Restoring zone files from last backup...\n"
fi

# Rollback mode logic
if $ROLLBACK; then
    if [[ ! -d "$BACKUP_DIR" ]]; then
        echo -e "${RED}Error:${RESET} No backup folder found at $BACKUP_DIR"
        exit 1
    fi
    for bak in "$BACKUP_DIR"/*.db.bak; do
        orig="${NAMED_DIR}/$(basename "$bak" .bak)"
        cp "$bak" "$orig"
        echo -e "${CYAN}Restored:${RESET} $(basename "$orig")"
    done
    /scripts/rebuilddnsconfig && /scripts/restartsrv_named
    echo -e "\n${GREEN}Rollback complete!${RESET}"
    exit 0
fi

# Prompt for NS entries
read_ns_entries() {
    local label=$1
    local -n arr=$2
    echo -n "Enter the number of $label NS entries: "
    read count
    for ((i = 1; i <= count; i++)); do
        echo -n "Enter $label NS entry $i: "
        read entry
        arr+=("$entry")
    done
}

# Collect NS entries
declare -a OLD_NS NEW_NS
read_ns_entries "old" OLD_NS
read_ns_entries "new" NEW_NS

# Get domain to exclude from new NS
EXCLUDED_DOMAIN=$(echo "${NEW_NS[0]}" | awk -F'.' '{print $(NF-1)"."$NF}')

echo -e "\n${CYAN}New NS domain will be excluded:${RESET} $EXCLUDED_DOMAIN\n"

# Create backup directory
mkdir -p "$BACKUP_DIR"
touch "$LOG_FILE"

# Process zone files
ZONES=$(grep -lr "${OLD_NS[0]}" $NAMED_DIR | grep -v "$EXCLUDED_DOMAIN" | awk -F/ '{print $NF}' | sed 's/\.db$//')

for domain in $ZONES; do
    total=$((total + 1))
    zone_file="$NAMED_DIR/$domain.db"
    backup_file="$BACKUP_DIR/$domain.db.bak"

    if [[ ! -f "$zone_file" ]]; then
        echo -e "${RED}Missing:${RESET} $zone_file" | tee -a "$LOG_FILE"
        failed=$((failed + 1))
        continue
    fi

    echo -e "${BOLD}Processing:${RESET} $domain"

    cp "$zone_file" "$backup_file"

    for i in "${!OLD_NS[@]}"; do
        OLD=${OLD_NS[$i]}
        NEW=${NEW_NS[$i]}
        if $DRY_RUN; then
            echo -e "  Would replace: ${YELLOW}$OLD${RESET} → ${GREEN}$NEW${RESET}"
        else
            sed -i "s/$OLD/$NEW/g" "$zone_file"
        fi
    done

    if ! $DRY_RUN; then
        sed -i '/Serial/s/[0-9]\{10,\}/'$(date +%Y%m%d%H%M%S)'/' "$zone_file"
        echo -e "  ${GREEN}✔ Updated${RESET} and serial bumped"
        echo "$domain - updated" >> "$LOG_FILE"
        updated=$((updated + 1))
    else
        echo "$domain - would update" >> "$LOG_FILE"
        skipped=$((skipped + 1))
    fi
done

# Rebuild config and restart once if not dry-run
if ! $DRY_RUN; then
    /scripts/rebuilddnsconfig && /scripts/restartsrv_named
fi

# Summary
echo -e "\n${BOLD}${CYAN}──────────── Summary ─────────────${RESET}"
echo -e "${BOLD}Total domains processed:${RESET}     $total"
echo -e "${BOLD}Updated successfully:${RESET}        $updated"
echo -e "${BOLD}Skipped (dry-run or excluded):${RESET} $skipped"
echo -e "${BOLD}Failed:${RESET}                      $failed"
echo -e "${BOLD}Backup location:${RESET}             $BACKUP_DIR"
echo -e "${BOLD}Log file:${RESET}                    $LOG_FILE"
echo -e "${CYAN}────────────────────────────────────${RESET}\n"

